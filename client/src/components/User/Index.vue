<template>
  <div class="user-list-container">
    <h1>Get All Users</h1>
    <div class="create-user-btn">
      <button v-on:click="navigateTo('/user/create')" class="btn-create">สร้างผู้ใช้</button>
    </div>
    <hr>
    <div v-if="users.length" class="user-list">
      <div><b>จำนวนผู้ใช้งาน:</b> {{ users.length }}</div>
      <div v-for="user in users" v-bind:key="user.id" class="user-card">
        <div><b>id:</b> {{ user.id }}</div>
        <div><b>ชื่อผู้ใช้:</b> {{ user.name }} {{ user.lastname }}</div>
        <div><b>อีเมล:</b> {{ user.email }}</div>
        <div><b>status:</b> {{ user.status }}</div>
        <div><b>type:</b> {{ user.type }}</div>
        <div class="user-actions">
          <button v-on:click="navigateTo('/user/'+user.id)" class="btn-view">ดูข้อมูล</button>
          <button v-on:click="navigateTo('/user/edit/'+user.id)" class="btn-edit">แก้ไขข้อมูล</button>
          <button v-on:click="deleteUser(user)" class="btn-delete">ลบข้อมูล</button>
        </div>
        <hr>
      </div>
    </div>
    <div class="logout-btn">
      <button v-on:click="logout" class="btn-logout">Logout</button>
    </div>
  </div>
</template>

<script>
import UsersService from "@/services/UsersService";
export default {
  data() {
    return {
      users: []
    }
  },
  async created() {
    try {
      this.users = (await UsersService.index()).data;
    } catch (err) {
      console.log(err);
    }
  },
  methods: {
    logout() {
      this.$store.dispatch('setToken', null)
      this.$store.dispatch('setUser', null)
      this.$router.push({
        name: 'login'
      })
    },
    navigateTo(route) {
      this.$router.push(route);
    },
    async deleteUser(user) {
      let result = confirm("คุณต้องการลบข้อมูลใช่หรือไม่?");
      if (result) {
        try {
          await UsersService.delete(user);
          this.refreshData();

        } catch (err) {
          console.log(err);
        }
      }
    },
    async refreshData() {
      try {
        this.users = (await UsersService.index()).data;
      } catch (err) {
        console.log(err);
      }
    }
  }
};
</script>

<style scoped>
.user-list-container {
  max-width: 800px;
  margin: 50px auto;
  padding: 20px;
  background-image: url('/path/to/travel-theme-background.jpg');
  background-size: cover;
  border-radius: 10px;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
  font-family: 'Arial', sans-serif;
}

h1 {
  text-align: center;
  color: #2980b9;
}

.create-user-btn, .logout-btn {
  text-align: center;
  margin-bottom: 20px;
}

.btn-create, .btn-logout {
  background-color: #3498db;
  color: white;
  padding: 10px 20px;
  border: none;
  border-radius: 5px;
  cursor: pointer;
}

.btn-create:hover, .btn-logout:hover {
  background-color: #2980b9;
}

.user-list {
  background-color: rgba(255, 255, 255, 0.8);
  padding: 20px;
  border-radius: 10px;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
}

.user-card {
  margin-bottom: 20px;
  padding: 15px;
  background-color: white;
  border: 1px solid #ddd;
  border-radius: 10px;
}

.user-actions {
  display: flex;
  justify-content: space-around;
  margin-top: 10px;
}

.btn-view, .btn-edit, .btn-delete {
  padding: 5px 10px;
  border: none;
  border-radius: 5px;
  cursor: pointer;
  font-size: 14px;
}

.btn-view {
  background-color: #2ecc71;
  color: white;
}

.btn-edit {
  background-color: #f39c12;
  color: white;
}

.btn-delete {
  background-color: #e74c3c;
  color: white;
}

.btn-view:hover {
  background-color: #27ae60;
}

.btn-edit:hover {
  background-color: #e67e22;
}

.btn-delete:hover {
  background-color: #c0392b;
}
</style>
