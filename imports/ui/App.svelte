<script>
  import { Meteor } from "meteor/meteor";
  import { Tracker } from "meteor/tracker";
  import { onMount, onDestroy } from "svelte";
  import { TasksCollection } from "../api/TasksCollection";
  import "/imports/api/TasksMethods";
  import Task from "./Task.svelte";
  import Login from "./Login.svelte";

  let newTask = '';
  let hideCompleted = false;

  // Reactive state
  let handle;
  let subIsReady = false;
  let currentUser = null;
  let tasks = [];
  let incompleteCount = 0;

  let computation;

  $: incompleteDisplay = incompleteCount > 0 ? `(${incompleteCount})` : '';

  onMount(() => {
    handle = Meteor.subscribe("tasks");

    computation = Tracker.autorun(() => {
      subIsReady = handle.ready();
      currentUser = Meteor.user();

      if (currentUser) {
        const filter = hideCompleted ? { isChecked: { $ne: true } } : {};
        tasks = TasksCollection.find(filter, { sort: { createdAt: -1, _id: -1 } }).fetch();
        incompleteCount = TasksCollection.find({ isChecked: { $ne: true } }).count();
      } else {
        tasks = [];
        incompleteCount = 0;
      }
    });

    return () => {
      computation?.stop?.();
      handle?.stop?.();
    };
  });

  onDestroy(() => {
    computation?.stop?.();
    handle?.stop?.();
  });

  async function addTask(event) {
    event.preventDefault();
    if (newTask.trim()) {
      await Meteor.callAsync("tasks.insert", {
        text: newTask,
        createdAt: new Date(),
      });
      newTask = '';
    }
  }

  function toggleHideCompleted() {
    hideCompleted = !hideCompleted;
  }
</script>

<div class="app">
  <header>
    <div class="app-bar">
      <div class="app-header">
        <h1>📝️ To Do List {incompleteDisplay}</h1>
      </div>
    </div>
  </header>

  <div class="main">
    {#if currentUser}
      <div class="user" on:click={() => Meteor.logout()}>
        {currentUser.username} 🚪
      </div>

      <form class="task-form" on:submit={addTask}>
        <input type="text" placeholder="Type to add new tasks" bind:value={newTask} />
        <button type="submit">Add Task</button>
      </form>

      <div class="filter">
        <button on:click={toggleHideCompleted}>
          {#if hideCompleted}
            Show All
          {:else}
            Hide Completed
          {/if}
        </button>
      </div>

      <ul class="tasks">
        {#if subIsReady}
          {#each tasks as task (task._id)}
            <Task {task} />
          {/each}
        {:else}
          <div>Loading ...</div>
        {/if}
      </ul>
    {:else}
      <Login />
    {/if}
  </div>
</div>
