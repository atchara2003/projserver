<template>
  <div class="edit-user-container">
    <h1>Edit User</h1>
    <form v-on:submit.prevent="editUser" class="user-form">
      <div class="form-group">
        <label for="name">Name:</label>
        <input type="text" id="name" v-model="user.name" placeholder="Enter your name">
      </div>
      <div class="form-group">
        <label for="lastname">Last Name:</label>
        <input type="text" id="lastname" v-model="user.lastname" placeholder="Enter your last name">
      </div>
      <div class="form-group">
        <label for="email">Email:</label>
        <input type="email" id="email" v-model="user.email" placeholder="Enter your email">
      </div>
      <div class="form-group">
        <label for="password">Password:</label>
        <input type="password" id="password" v-model="user.password" placeholder="Enter your password">
      </div>
      <div class="form-actions">
        <button type="submit" class="btn-submit">Edit User</button>
      </div>
    </form>
  </div>
</template>

<script>
import UsersService from '../../services/UsersService';
export default {
  data() {
    return {
      user: {
        name: '',
        lastname: '',
        email: '',
        password: '',
        status: 'active'
      }
    }
  },
  async created() {
    try {
      var userId = this.$route.params.userId;
      this.user = (await UsersService.show(userId)).data;
    } catch (err) {
      console.log(err);
    }
  },
  methods: {
    async editUser() {
      try {
        await UsersService.put(this.user);
        this.$router.push('/users');
      } catch (err) {
        console.log(err);
      }
    }
  }
}
</script>

<style scoped>
.edit-user-container {
  max-width: 600px;
  margin: 50px auto;
  background-color: #f0f8ff;
  padding: 20px;
  border-radius: 10px;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
  font-family: 'Arial', sans-serif;
}

h1 {
  text-align: center;
  color: #2980b9;
  margin-bottom: 20px;
}

.user-form {
  display: flex;
  flex-direction: column;
}

.form-group {
  margin-bottom: 15px;
}

.form-group label {
  font-weight: bold;
  color: #2c3e50;
}

.form-group input {
  width: 100%;
  padding: 10px;
  border: 1px solid #ccc;
  border-radius: 5px;
  margin-top: 5px;
}

.form-group input:focus {
  outline: none;
  border-color: #2980b9;
}

.form-actions {
  display: flex;
  justify-content: center;
}

.btn-submit {
  background-color: #2980b9;
  color: white;
  padding: 10px 20px;
  border: none;
  border-radius: 5px;
  cursor: pointer;
}

.btn-submit:hover {
  background-color: #3498db;
}
</style>
