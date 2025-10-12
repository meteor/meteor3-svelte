<script>
  import { Meteor } from 'meteor/meteor';

  let username = '';
  let password = '';

  async function login(event) {
    event.preventDefault();
    await Meteor.loginWithPassword(username, password);
  }
</script>

<form class="login-form" on:submit={login}>
  <div>
    <label for="username">Username</label>
    <input
      type="text"
      placeholder="Username"
      name="username"
      required
      bind:value={username}
    />
  </div>

  <div>
    <label for="password">Password</label>
    <input
      type="password"
      placeholder="Password"
      name="password"
      required
      bind:value={password}
    />
  </div>
  <div>
    <button type="submit">Log In</button>
  </div>
</form>