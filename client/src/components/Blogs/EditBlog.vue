<template>
  <div class="container">
    <h1 class="title">แก้ไขการเดินทาง</h1>
    <form v-on:submit.prevent="editBlog" class="form-edit">
      <div class="form-group">
        <label>ชื่อสถานที่:</label>
        <input type="text" v-model="blog.title" class="input-field" />
      </div>

      <transition name="fade">
        <div class="thumbnail-pic" v-if="blog.thumbnail != 'null'">
          <img :src="BASE_URL + blog.thumbnail" alt="thumbnail" />
        </div>
      </transition>

      <div class="form-group">
        <label>อัปโหลดรูปภาพการเดินทาง:</label>
        <form enctype="multipart/form-data" novalidate>
          <div class="dropbox">
            <input
              type="file"
              multiple
              :name="uploadFieldName"
              :disabled="isSaving"
              @change="
                filesChange($event.target.name, $event.target.files);
                fileCount = $event.target.files.length;
              "
              accept="image/*"
              class="input-file"
            />
            <p v-if="isInitial">ลากไฟล์ของคุณที่นี่ หรือคลิกเพื่อเลือกไฟล์</p>
            <p v-if="isSaving">กำลังอัปโหลด {{ fileCount }} ไฟล์...</p>
            <p v-if="isSuccess">อัปโหลดสำเร็จ</p>
          </div>
        </form>
      </div>

      <transition-group tag="ul" class="pictures">
        <li v-for="picture in pictures" v-bind:key="picture.id" class="picture-item">
          <img
            :src="BASE_URL + picture.name"
            alt="picture image"
            class="uploaded-image"
          />
          <div class="button-group">
            <button class="btn" v-on:click.prevent="useThumbnail(picture.name)">
              ตั้งเป็นรูปภาพปก
            </button>
            <button class="btn btn-danger" v-on:click.prevent="delFile(picture)">
              ลบ
            </button>
          </div>
        </li>
      </transition-group>

      <div class="form-group">
        <label>วันที่เดินทาง:</label>
        <input type="date" v-model="blog.category" class="input-field" />
      </div>

      <div class="form-group">
        <label>รายละเอียดกิจกรรม:</label>
        <input type="text" v-model="blog.status" class="input-field" />
      </div>

      <div class="form-group button-container">
        <button type="submit" class="btn btn-primary">อัปเดต</button>
        <button v-on:click="navigateTo('/blogs')" class="btn btn-secondary">
          กลับ
        </button>
      </div>
    </form>
  </div>
</template>
<script>
import BlogsService from "@/services/BlogsService";
import VueCkeditor from "vue-ckeditor2";
import UploadService from "../../services/UploadService";

const STATUS_INITIAL = 0,
  STATUS_SAVING = 1,
  STATUS_SUCCESS = 2,
  STATUS_FAILED = 3;

export default {
  components: { VueCkeditor },
  data() {
    return {
      BASE_URL: "http://localhost:8081/assets/uploads/",
      error: null,
      // uploadedFiles: [],
      uploadError: null,
      currentStatus: null,
      uploadFieldName: "userPhoto",
      uploadedFileNames: [],
      pictures: [],
      pictureIndex: 0,
      blog: {
        title: "",
        thumbnail: "null",
        pictures: "null",
        content: "",
        category: "",
        status: "",
      },
      config: {
        toolbar: [
          ["Bold", "Italic", "Underline", "Strike", "Subscript", "Superscript"],
        ],
        height: 300,
      },
    };
  },
  methods: {
    async delFile(material) {
      let result = confirm("Want to delete?");
      if (result) {
        let dataJSON = {
          filename: material.name,
        };

        await UploadService.delete(dataJSON);
        for (var i = 0; i < this.pictures.length; i++) {
          if (this.pictures[i].id === material.id) {
            this.pictures.splice(i, 1);
            this.materialIndex--;
            break;
          }
        }
      }
    },
    async editBlog() {
      try {
        await BlogsService.put(this.blog);
        this.$router.push({
          name: "blogs",
        });
      } catch (err) {
        console.log(err);
      }
    },
    onBlur(editor) {
      console.log(editor);
    },
    onFocus(editor) {
      console.log(editor);
    },
    navigateTo(route) {
      console.log(route);
      this.$router.push(route);
    },
    wait(ms) {
      return (x) => {
        return new Promise((resolve) => setTimeout(() => resolve(x), ms));
      };
    },
    reset() {
      // reset form to initial state
      this.currentStatus = STATUS_INITIAL;
      // this.uploadedFiles = []
      this.uploadError = null;
      this.uploadedFileNames = [];
    },
    async save(formData) {
      // upload data to the server
      try {
        this.currentStatus = STATUS_SAVING;
        await UploadService.upload(formData);
        this.currentStatus = STATUS_SUCCESS;

        // update image uploaded display
        let pictureJSON = [];
        this.uploadedFileNames.forEach((uploadFilename) => {
          let found = false;
          for (let i = 0; i < this.pictures.length; i++) {
            if (this.pictures[i].name == uploadFilename) {
              found = true;
              break;
            }
          }
          if (!found) {
            this.pictureIndex++;
            let pictureJSON = {
              id: this.pictureIndex,
              name: uploadFilename,
            };
            this.pictures.push(pictureJSON);
          }
        });
        this.clearUploadResult();
      } catch (error) {
        console.log(error);
        this.currentStatus = STATUS_FAILED;
      }
    },
    filesChange(fieldName, fileList) {
      // handle file changes
      const formData = new FormData();
      if (!fileList.length) return;
      // append the files to FormData
      Array.from(Array(fileList.length).keys()).map((x) => {
        formData.append(fieldName, fileList[x], fileList[x].name);
        this.uploadedFileNames.push(fileList[x].name);
      });
      // save it
      this.save(formData);
    },
    clearUploadResult: function () {
      setTimeout(() => this.reset(), 5000);
    },
    useThumbnail(filename) {
      console.log(filename);
      this.blog.thumbnail = filename;
    },
  },
  computed: {
    isInitial() {
      return this.currentStatus === STATUS_INITIAL;
    },
    isSaving() {
      return this.currentStatus === STATUS_SAVING;
    },
    isSuccess() {
      return this.currentStatus === STATUS_SUCCESS;
    },
    isFailed() {
      return this.currentStatus === STATUS_FAILED;
    },
  },
  components: {
    VueCkeditor,
  },
  async created() {
    this.currentStatus = STATUS_INITIAL;
    this.config.toolbar = [
      {
        name: "document",
        items: [
          "Source",
          "-",
          "Save",
          "NewPage",
          "Preview",
          "Print",
          "-",
          "Templates",
        ],
      },
      {
        name: "clipboard",
        items: [
          "Cut",
          "Copy",
          "Paste",
          "PasteText",
          "PasteFromWord",
          "-",
          "Undo",
          "Redo",
        ],
      },
      {
        name: "editing",
        items: ["Find", "Replace", "-", "SelectAll", "-", "Scayt"],
      },
      {
        name: "forms",
        items: [
          "Form",
          "Checkbox",
          "Radio",
          "TextField",
          "Textarea",
          "Select",
          "Button",
          "ImageButton",
          "HiddenField",
        ],
      },
      "/",
      {
        name: "basicstyles",
        items: [
          "Bold",
          "Italic",
          "Underline",
          "Strike",
          "Subscript",
          "Superscript",
          "-",
          "CopyFormatting",
          "RemoveFormat",
        ],
      },
      {
        name: "paragraph",
        items: [
          "NumberedList",
          "BulletedList",
          "-",
          "Outdent",
          "Indent",
          "-",
          "Blockquote",
          "CreateDiv",
          "-",
          "JustifyLeft",
          "JustifyCenter",
          "JustifyRight",
          "JustifyBlock",
          "-",
          "BidiLtr",
          "BidiRtl",
          "Language",
        ],
      },
      { name: "links", items: ["Link", "Unlink", "Anchor"] },
      {
        name: "insert",
        items: [
          "Image",
          "Flash",
          "Table",
          "HorizontalRule",
          "Smiley",
          "SpecialChar",
          "PageBreak",
          "Iframe",
          "InsertPre",
        ],
      },
      "/",
      { name: "styles", items: ["Styles", "Format", "Font", "FontSize"] },
      { name: "colors", items: ["TextColor", "BGColor"] },
      { name: "tools", items: ["Maximize", "ShowBlocks"] },
      { name: "about", items: ["About"] },
    ];

    try {
      let blogId = this.$route.params.blogId;
      this.blog = (await BlogsService.show(blogId)).data;
      this.pictures = JSON.parse(this.blog.pictures);
      this.pictureIndex = this.pictures.length;
    } catch (error) {
      console.log(error);
    }
  },
  async mounted() {
    try {
      let blogId = this.$route.params.blogId;
      this.blog = (await BlogsService.show(blogId)).data;
      this.pictures = JSON.parse(this.blog.pictures);
    } catch (error) {
      console.log(error);
    }
  },
};
</script>
<style scoped>
.container {
  width: 100%;
  max-width: 800px;
  margin: 0 auto;
  padding: 20px;
  background: #d1d1d1;
  border-radius: 10px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
}

.title {
  font-size: 2em;
  margin-bottom: 20px;
  color: #628391;
  text-align: center;
}

.form-group {
  margin-bottom: 15px;
}

.label {
  font-weight: bold;
  margin-bottom: 5px;
}

.input-field {
  width: 100%;
  padding: 10px;
  border: 1px solid #ccc;
  border-radius: 5px;
}

.dropbox {
  outline: 2px dashed grey;
  outline-offset: -10px;
  background: lemonchiffon;
  color: dimgray;
  padding: 10px;
  min-height: 200px;
  position: relative;
  cursor: pointer;
  margin-bottom: 15px;
}

.dropbox:hover {
  background: khaki;
}

.input-file {
  opacity: 0;
  width: 100%;
  height: 200px;
  position: absolute;
}

.pictures {
  list-style: none;
  padding: 0;
  margin: 0;
  display: flex;
  flex-wrap: wrap;
}

.picture-item {
  margin-right: 20px;
  margin-bottom: 20px;
  text-align: center;
}

.uploaded-image {
  max-width: 180px;
  margin-bottom: 5px;
}

.button-group {
  display: flex;
  justify-content: center;
}

.btn {
  margin-right: 10px;
  padding: 8px 12px;
  border: none;
  background-color: #007bff;
  color: white;
  border-radius: 5px;
  cursor: pointer;
  font-size: 1em;
}

.btn-secondary {
  background-color: #6c757d;
}

.btn-danger {
  background-color: #dc3545;
}

.button-container {
  text-align: center;
  margin-top: 20px;
}

.clearfix {
  clear: both;
}

.thumbnail-pic img {
  width: 200px;
  margin-bottom: 15px;
}
</style>