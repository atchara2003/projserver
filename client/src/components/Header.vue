<template>
  <div class="header-container">
    <div class="nv-navbar">
      <ul class="nav">
        <li><router-link :to="{ name: 'blogs' }">บันทึกการเดินทาง</router-link></li>
        <li><router-link :to="{ name: 'users' }">ผู้ใช้งาน</router-link></li>
        <li v-if="isLogin()"><a href="#" v-on:click="logout">ออกจากระบบ</a></li>
        <li v-else><router-link :to="{ name: 'login' }">เข้าสู่ระบบ</router-link></li>
      </ul>
    </div>
  </div>
</template>


<script>

export default {

  methods:{
    isLogin(){
      return this.$store.getters.isUserLoggedIn;
    },
    logout(){
      this.$store.dispatch('logout');
      this.$router.push({name: 'login'});
    }
  }
};
</script>
<style scoped>
/* ปรับแต่ง header ให้อยู่ตรงกลางและดูสะอาด */
.header-container {
  display: flex;
  justify-content: center;
  align-items: center;
  background: url('https://cdn.pixabay.com/photo/2021/11/21/21/14/mountain-6815304_1280.jpg') no-repeat center center; /* เพิ่มภาพพื้นหลัง */
  background-size: cover;
  height: 250px; /* กำหนดความสูงให้สูงขึ้นเพื่อให้ดูดี */
  color: white;
}

.nv-navbar {
  position: fixed; /* ยึด navbar ไว้ที่ขอบบน */
  top: 0;
  left: 0;
  width: 100%; /* ให้ความกว้างเต็มหน้าจอ */
  background-color: rgba(0, 0, 0, 0.7);
  padding: 15px;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
}

.nv-navbar .nav {
  list-style: none;
  display: flex;
  justify-content: space-around; /* ให้รายการกระจายตัวให้เต็มพื้นที่ */
  margin: 0;
  padding: 0;
}

.nv-navbar .nav li {
  margin: 0;
}

.nv-navbar .nav li a {
  color: white;
  text-decoration: none;
  font-size: 18px;
  font-weight: bold;
  padding: 10px 20px;
  transition: background-color 0.3s, color 0.3s;
  border-radius: 5px;
}

.nv-navbar .nav li a:hover {
  background-color: rgba(255, 255, 255, 0.3); /* ทำให้มีสีขาวอ่อนเมื่อ hover */
}

.nv-navbar .nav li a.router-link-active {
  background-color: #FFD700; /* สีทองเมื่อเลือกเมนู */
  color: black;
}

@media (max-width: 768px) {
  .nv-navbar {
    flex-direction: column;
    align-items: center;
  }
  .nv-navbar .nav {
    flex-direction: column;
  }
  .nv-navbar .nav li {
    margin-bottom: 10px;
  }
}
</style>

