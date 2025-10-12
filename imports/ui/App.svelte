<script>
  import { Meteor } from "meteor/meteor";
  import { TasksCollection } from "../api/TasksCollection";
  import '/imports/api/TasksMethods.js'; // this import in this client UI allows for optimistic execution
  import Task from "./Task.svelte";

  let newTask = '';

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

  $m: handle = Meteor.subscribe("tasks");
  $m: subIsReady = handle.ready();
  $m: tasks = TasksCollection.find({}, { sort: { createdAt: -1, _id: -1 } }).fetch();
</script>

<div class="container">
  <header>
    <h1>Todo List</h1>
  </header>

  <form class="task-form" on:submit={addTask}>
    <input type="text" placeholder="Type to add new tasks" bind:value={newTask} />
    <button type="submit">Add Task</button>
  </form>

  <ul>
    {#if subIsReady}
      {#each tasks as task (task._id)}
        <Task {task} />
      {/each}
    {:else}
      <div>Loading ...</div>
    {/if}
  </ul>
</div>