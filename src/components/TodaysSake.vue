<template>
  <v-app id="app">
    <v-container>
      <v-row>
        <v-col cols="12">
          <p class="text-h2">今日のお酒ジェネレーター</p>
          <br />
          <p class="text-h3">お酒の情報と画像を入力</p>
          <p class="text-left">Left aligned text on all viewport sizes.</p>
        </v-col>
        <v-col cols="6">
          <v-form>
            <v-text-field
              v-model="sakeCategory"
              label="酒の種類"
              single-line
              full-width
              hide-details
              @input="drawIntroduction"
            ></v-text-field>
            <v-text-field
              v-model="sakeName"
              label="酒の名前"
              single-line
              full-width
              hide-details
              @input="drawIntroduction"
            ></v-text-field>
            <v-divider></v-divider>
            <v-textarea
              v-model="sakeDescription"
              label="お酒の説明"
              counter
              maxlength="200"
              full-width
              single-line
              @input="drawIntroduction"
            ></v-textarea>
          </v-form>
        </v-col>
        <v-col cols="6">
          <div class="main">
            <input
              style="display: none"
              ref="input"
              class="file-button"
              type="file"
              @change="upload"
            />
            <v-btn depressed @click="selectImageClick">画像アップロード</v-btn>
            <ul v-if="fileErrorMessages.length > 0" class="error-messages">
              <li v-for="(message, index) in fileErrorMessages" :key="index">
                {{ message }}
              </li>
            </ul>

            <div v-if="imgSrc != ''">
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
            <v-btn type="button" depressed block @click="cropImage"
              >トリミングする</v-btn
            >
          </div>
        </v-col>
        <v-col cols="12">
          <p class="text-h3">プレビュー</p>
          <!-- 合成画像領域（Canvas） -->
          <canvas id="canvas" width="1280" height="720" ref="canvas"></canvas>
        </v-col>
        <!-- 非表示サンプル酒画像 -->
        <img
          alt="example_sake"
          src="../assets/example_sake.jpg"
          style="display: none"
          ref="examplesake"
        />
      </v-row>
    </v-container>
  </v-app>
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
      sakeCategory: "",
      sakeName: "",
      sakeDescription: "",
    };
  },
  mounted() {
    // サンプル画像読み込み
    this.$nextTick(function () {
      this.imgSrc = this.$refs.examplesake.src;
      // todo : なくてもよさそう？
      // this.$refs.cropper.replace(this.$refs.examplesake.src);
      // this.drawCanvas();
    });
  },
  methods: {
    //酒の紹介を描画する
    drawIntroduction() {
      this.canvasContext = this.$refs.canvas.getContext("2d");
      this.canvasContext.font = "32px serif";
      this.canvasContext.fillStyle = "#404040";

      this.canvasContext.fillText(this.sakeCategory, 100, 100);
      this.canvasContext.fillText(this.sakeName, 100, 200);
      this.canvasContext.fillText(this.sakeDescription, 100, 300);
    },
    // imput属性のメソッド実行
    selectImageClick() {
      this.$refs.input.click();
    },
    // 画像アップロード実行
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
    // アップロードファイルチェック
    checkFile(file) {
      let result = true;
      this.fileErrorMessages = [];
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
      // ファイル上限 = 5MB
      const SIZE_LIMIT = 5000000;
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
          740, //切り出されるCanvas内でのX座標指定
          0, //切り出されるCanvas内でのY座標指定
          data["vectorX"] * 540, //切り出される画像の横幅
          data["vectorY"] * 720 //切り出される画像の縦幅
        );
      };
    },
  },
};
</script>

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
