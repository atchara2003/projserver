<template>
    <div class="blog-container">
        <h1 class="blog-title">บันทึกการเดินทาง</h1>
        <p class="blog-detail">รหัส: {{ blog.id }}</p>
        <p class="blog-detail">ชื่อสถานที่: {{ blog.title }}</p>
        <p class="blog-detail">วันที่ไป: {{ blog.content }}</p>

        <transition name="fade">
                <div v-if="blog.thumbnail && blog.thumbnail !== 'null'" class="thumbnail-pic">
                    <img :src="BASE_URL + blog.thumbnail" :alt="blog.title" :title="blog.title" />
                </div>
                <p v-else>ไม่พบภาพ</p>
            </transition>

        <p class="blog-detail">หมวดหมู่: {{ blog.category }}</p>
        <p class="blog-detail">รายละเอียดกิจกรรม: {{ blog.status }}</p>
        <div class="button-container">
            <button class="edit-button" v-on:click="navigateTo('/blog/edit/' + blog.id)">แก้ไข blog</button>
            <button class="back-button" v-on:click="navigateTo('/blogs')">กลับ</button>
        </div>
    </div>
</template>

<script>
import BlogsService from '@/services/BlogsService'

export default {
    data() {
        return {
            blog: null,
            BASE_URL: 'http://localhost:8081/assets/uploads/' // ตั้งค่า BASE_URL ให้ถูกต้อง
        }
    },
    async created() {
        try {
            const blogId = this.$route.params.blogId
            this.blog = (await BlogsService.show(blogId)).data
            console.log('Pictures:', this.blog.pictures) // ตรวจสอบว่ามีรูปภาพหรือไม่
        } catch (error) {
            console.error('ไม่สามารถโหลดข้อมูลบล็อก:', error)
        }
    },
    methods: {
    navigateTo(route) {
        this.$router.push(route);
    },
    async deleteBlog(blog) {
        let result = confirm("ต้องการลบสินค้านี้หรือไม่?");
        if (result) {
            try {
                await BlogsService.delete(blog);
                this.$router.push('/blog'); // หลังจากลบสำเร็จ จะกลับไปที่หน้า index ของ blog
            } catch (err) {
                console.log(err);
            }
        }
    },
    async refreshData() {
        this.blogs = (await BlogsService.index()).data;
    }
}

}
</script>

<style scoped>
.blog-container {
    background: url('/assets/travel-background.jpg') no-repeat center center;
    background-size: cover;
    padding: 20px;
    border-radius: 10px;
    box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
    max-width: 800px;
    margin: 20px auto;
    color: #333;
}

.blog-title {
    font-family: 'Sarabun', sans-serif;
    font-size: 36px;
    color: #0066cc;
    text-align: center;
    margin-bottom: 20px;
}

.blog-detail {
    font-family: 'Sarabun', sans-serif;
    font-size: 18px;
    margin: 10px 0;
    color: #555;
}

.pictures-title {
    font-family: 'Sarabun', sans-serif;
    font-size: 22px;
    margin-bottom: 10px;
    color: #333;
}

.pictures-gallery {
    display: flex;
    flex-wrap: wrap;
    gap: 10px;
    justify-content: center;
}

.picture-container {
    width: 150px;
    height: 150px;
    overflow: hidden;
    border-radius: 8px;
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}

.blog-picture {
    width: 100%;
    height: 100%;
    object-fit: cover;
}

.no-picture {
    font-family: 'Sarabun', sans-serif;
    font-size: 16px;
    color: #999;
}

.button-container {
    display: flex;
    justify-content: space-between;
    margin-top: 20px;
}

.edit-button, .back-button {
    font-family: 'Sarabun', sans-serif;
    font-size: 16px;
    padding: 10px 20px;
    border: none;
    border-radius: 5px;
    cursor: pointer;
}

.edit-button {
    background-color: #0066cc;
    color: #fff;
}

.back-button {
    background-color: #f44336;
    color: #fff;
}

.edit-button:hover, .back-button:hover {
    opacity: 0.8;
}
</style>
