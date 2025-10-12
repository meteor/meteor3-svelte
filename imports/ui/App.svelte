<script>
  import { Meteor } from "meteor/meteor";
  import { TasksCollection } from "../api/TasksCollection";

  $m: handle = Meteor.subscribe("tasks");
  $m: subIsReady = handle.ready();
  $m: tasks = TasksCollection.find().fetch();
</script>

<div class="container">
  <header>
    <h1>Todo List</h1>
  </header>

  <ul>
    {#if subIsReady}
      {#each tasks as task (task._id)}
        <li>{task.text}</li>
      {/each}
    {:else}
      <div>Loading ...</div>
    {/if}
  </ul>
</div>
