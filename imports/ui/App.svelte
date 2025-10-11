<script>
  import { Meteor } from "meteor/meteor";
  
  let tasks = [
    { text: 'This is task 1' },
    { text: 'This is task 2' },
    { text: 'This is task 3' },
  ];
</script>

<div class="container">
  <header>
    <h1>Todo List</h1>
  </header>

  <ul>
    {#each tasks as task (task.text)}
      <li>{task.text}</li>
    {/each}
  </ul>
</div>
