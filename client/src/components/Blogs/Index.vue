<template>
    <div class="travel-blog-container">
        <div class="header">
            <h2>รายการเดินทาง </h2>
            
        </div>
        <br>
        <button class="btn btn-create" v-on:click="navigateTo('/blog/create')">เพิ่มรายการ</button>
        <div class="blog-stats">
            <p>จำนวนสถานที่ที่บันทึกไว้: {{ blogs.length }}</p>
        </div>

        <div class="blog-list">
            <div v-for="blog in blogs" v-bind:key="blog.id" class="blog-card">
                <div class="blog-content">
                    <h3 class="blog-title">ชื่อสถานที่: {{ blog.title }}</h3>

                    <!-- แสดงรูปภาพ Thumbnail -->
                    <transition name="fade">
                        <div class="thumbnail-pic" v-if="blog.thumbnail && blog.thumbnail !== 'null'">
                            <img :src="BASE_URL + blog.thumbnail" alt="Thumbnail" />
                        </div>
                    </transition>

                    <p class="blog-date"><strong>วันที่ไป:</strong> {{ blog.category }}</p>
                    <p class="blog-status"><strong>รายละเอียดกิจกรรม:</strong> {{ blog.status }}</p>

                    <div class="blog-actions">
                        <button class="btn btn-view" v-on:click="navigateTo('/blog/'+ blog.id)">ดู รายการ</button>
                        <button class="btn btn-edit" v-on:click="navigateTo('/blog/edit/'+ blog.id)">แก้ไข รายการ</button>
                        <button class="btn btn-delete" v-on:click="deleteBlog(blog)">ลบรายการ</button>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
import BlogsService from '@/services/BlogsService'
export default {
    data () {
        return {
            blogs: [],
            BASE_URL: 'http://localhost:8081/assets/uploads/' // ตั้งค่า BASE_URL ให้ถูกต้อง
        }
    },
    async created () {
        this.blogs = (await BlogsService.index()).data
    },
    methods: {
        navigateTo (route) {
            this.$router.push(route)
        },
        async deleteBlog (blog) {
            let result = confirm("ต้องการลบสินค้านี้หรือไม่?")
            if (result) {
                try {
                    await BlogsService.delete(blog)
                    this.refreshData()
                } catch (err) {
                    console.log(err)
                }
            }
        },
        async refreshData() {
            this.blogs = (await BlogsService.index()).data
        }
    }
}
</script>

<style scoped>
.travel-blog-container {
  font-family: 'Arial', sans-serif;
  padding: 20px;
  background-color: #f4f7fa;
}

.header {
  display: flex;
  justify-content: center;
  align-items: center;
  background-color: #007BFF;
  color: white;
  padding: 10px 20px;
  border-radius: 5px;
}

.header h2 {
  margin: 0;
  
  
}

.btn {
  padding: 10px 15px;
  border: none;
  border-radius: 5px;
  cursor: pointer;
  font-size: 14px;
}

.btn-create {
  background-color: #28a745;
  color: white;
}

.blog-stats {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin: 20px 0;
}

.blog-list {
  display: flex;
  justify-content: center;
  align-items: center;
  flex-wrap: wrap;
  gap: 20px;
}

.blog-card {
  background-color: white;
  border-radius: 10px;
  padding: 5%;
  width: calc(33.333% - 20px);
}

.blog-content {
  margin-bottom: 15px;
}

.blog-title {
  font-size: 18px;
  margin-bottom: 10px;
}

.blog-date, .blog-status {
  color: #6c757d;
  font-size: 14px;
}

.blog-actions {
  display: flex;
  justify-content: space-between;
}

.btn-view {
  background-color: #007BFF;
  color: white;
}

.btn-edit {
  background-color: #ffc107;
  color: white;
}

.btn-delete {
  background-color: #dc3545;
  color: white;
}
</style>
