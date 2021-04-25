<template>
  <div class="main">
    <input ref="file" class="file-button" type="file" @change="upload" />
    <button type="button" class="delete-button" @click="deleteImage">
      削除する
    </button>
    <ul v-if="fileErrorMessages.length > 0" class="error-messages">
      <li v-for="(message, index) in fileErrorMessages" :key="index">
        {{ message }}
      </li>
    </ul>
    <vue-cropper
      ref="cropper"
      :aspect-ratio="16 / 9"
      :src="imgSrc"
      preview=".preview"
    />
  </div>
</template>

<script>
import VueCropper from "vue-cropperjs";
import "cropperjs/dist/cropper.css";
export default {
  name: "TodaysSake",
  components: {
    VueCropper,
  },
  props: {
    value: {
      type: String,
      default: null,
    },
  },
  data() {
    return {
      imgSrc: "",
      file: null,
      fileErrorMessages: [],
    };
  },
  methods: {
    async upload(event) {
      const files = event.target.files || event.dataTransfer.files;
      const file = files[0];

      if (this.checkFile(file)) {
        const picture = await this.getBase64(file);
        this.$emit("input", picture);
      }
    },
    getBase64(file) {
      return new Promise((resolve, reject) => {
        const reader = new FileReader();
        reader.readAsDataURL(file);
        reader.onload = (event) => {
          this.imgSrc = event.target.result;
          // rebuild cropperjs with the updated source
          this.$refs.cropper.replace(event.target.result);
        };
        reader.onerror = (error) => reject(error);
      });
    },
    checkFile(file) {
      let result = true;
      this.fileErrorMessages = [];
      // ファイル上限 = 5MB
      const SIZE_LIMIT = 5000000;
      // キャンセルしたら処理中断
      if (!file) {
        result = false;
      }
      // jpeg か png ファイル以外は受け付けない
      if (file.type !== "image/jpeg" && file.type !== "image/png") {
        this.fileErrorMessages.push(
          "アップロードできるのは jpeg画像ファイル か png画像ファイルのみです。"
        );
        result = false;
      }
      // 上限ファイルより大きければ受け付けない
      if (file.size > SIZE_LIMIT) {
        this.fileErrorMessages.push(
          "アップロードできるファイルサイズは5MBまでです。"
        );
        result = false;
      }
      return result;
    },
    deleteImage() {
      this.$emit("input", null);
      this.$refs.file = null;
    },
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
