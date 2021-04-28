<template>
  <v-container>
    <v-row class="text-center">
      <v-col cols="12">
        <div class="main">
          <input ref="file" class="file-button" type="file" @change="upload" />
          <ul v-if="fileErrorMessages.length > 0" class="error-messages">
            <li v-for="(message, index) in fileErrorMessages" :key="index">
              {{ message }}
            </li>
          </ul>
          <button type="button" @click="cropImage" v-if="imgSrc != ''">
            Crop
          </button>
          <!-- Cropper / option : https://cly7796.net/blog/javascript/try-using-cropper-js/  -->
          <vue-cropper
            ref="cropper"
            :aspect-ratio="3 / 4"
            :src="imgSrc"
            :guides="true"
            :view-mode="2"
            :background="true"
            :rotatable="true"
            :img-style="{ width: '600', height: '400px' }"
          />
        </div>
        <canvas id="canvas" width="680" height="480" ref="canvas"></canvas>
      </v-col>
    </v-row>
  </v-container>
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
      canvasContext: {},
      imgSrc: "",
      cropImg: "",
      file: null,
      fileErrorMessages: [],
    };
  },
  methods: {
    async upload(event) {
      const files = event.target.files || event.dataTransfer.files;
      const file = files[0];

      if (this.checkFile(file)) {
        await this.getBase64(file);
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
    cropImage() {
      // cropした画像を保管
      this.cropImg = this.$refs.cropper.getCroppedCanvas().toDataURL();
      // cropした画像をImageのsrcとして設定
      var cropperData = new Image();
      cropperData.src = this.cropImg;
      // Canvasに画像を描画
      cropperData.onload = () => {
        var data = {
          x: Math.round(cropperData.x),
          y: Math.round(cropperData.y),
          width: Math.round(cropperData.width),
          height: Math.round(cropperData.height),
          vectorX: 1,
          vectorY: 1,
        };
        this.canvasContext = this.$refs.canvas.getContext("2d");
        this.canvasContext.drawImage(
          cropperData,
          data["x"],
          data["y"],
          data["width"],
          data["height"],
          0,
          0, //切り出されるCanvas内での座標指定
          data["vectorX"] * 680, //切り出される画像の横幅
          data["vectorY"] * 480 //切り出される画像の縦幅
        );
      };
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
