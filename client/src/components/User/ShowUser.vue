<template>
  <div class="show-user-container">
    <h1>Show User</h1>
    <div class="user-details">
      <p><b>ID:</b> {{ user.id }}</p>
      <p><b>Name:</b> {{ user.name }}</p>
      <p><b>Lastname:</b> {{ user.lastname }}</p>
      <p><b>Email:</b> {{ user.email }}</p>
      <p><b>Password:</b> {{ user.password }}</p>
      <p><b>Status:</b> {{ user.status }}</p>
      <p><b>Type:</b> {{ user.type }}</p>
      <p><b>Created At:</b> {{ user.createdAt }}</p>
    </div>
  </div>
</template>

<script>
import UsersService from "@/services/UsersService";

export default {
  data() {
    return {
      user: {}
    };
  },
  async created() {
    try {
      var userId = this.$route.params.userId;
      this.user = (await UsersService.show(userId)).data;
    } catch (err) {
      console.log(err);
    }
  }
};
</script>

<style scoped>
.show-user-container {
  max-width: 600px;
  margin: 50px auto;
  padding: 20px;
  background-color: #f0f8ff;
  border-radius: 10px;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
  font-family: 'Arial', sans-serif;
}

h1 {
  text-align: center;
  color: #2980b9;
}

.user-details {
  background-color: rgba(255, 255, 255, 0.9);
  padding: 20px;
  border-radius: 10px;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
}

p {
  font-size: 16px;
  color: #2c3e50;
  margin-bottom: 10px;
}

b {
  color: #2980b9;
}
</style>
