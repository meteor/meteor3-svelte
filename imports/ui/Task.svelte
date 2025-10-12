<script>
  import { Meteor } from "meteor/meteor";
  import "/imports/api/TasksMethods";

  export let task;

  async function toggleChecked() {
    await Meteor.callAsync("tasks.toggleChecked", { _id: task._id, isChecked: task.isChecked });
  }

  async function deleteTask() {
    await Meteor.callAsync("tasks.delete", { _id: task._id });
  }
</script>

<li>
  <label>
    <input type="checkbox" checked={task.isChecked} on:change={toggleChecked} />
    <span>{task.text}</span>
  </label>

  <button class="delete" on:click={deleteTask}>&times;</button>
</li>
