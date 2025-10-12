<script>
  import { Meteor } from "meteor/meteor";
  import { TasksCollection } from "../api/TasksCollection";
  import "/imports/api/TasksMethods";
  import Task from "./Task.svelte";
  import Login from "./Login.svelte"; 

  let newTask = '';
  let hideCompleted = false;

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

  $m: handle = Meteor.subscribe("tasks");
  $m: subIsReady = handle.ready();

  $m: currentUser = Meteor.user(); // Reactive current user  

  $m: tasks = currentUser // [!code highlight]
    ? TasksCollection.find( // [!code highlight]
        hideCompleted ? { isChecked: { $ne: true } } : {}, // [!code highlight]
        { sort: { createdAt: -1, _id: -1 } } // [!code highlight]
      ).fetch() // [!code highlight]
    : []; // [!code highlight]

  $m: incompleteCount = currentUser // [!code highlight]
    ? TasksCollection.find({ isChecked: { $ne: true } }).count() // [!code highlight]
    : 0; // [!code highlight]
  $m: incompleteDisplay = incompleteCount > 0 ? `(${incompleteCount})` : '';  
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