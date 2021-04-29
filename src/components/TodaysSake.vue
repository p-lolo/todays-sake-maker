<template>
  <v-app id="app">
    <v-container>
      <v-row align="center" justify="space-around">
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
              v-model="sakeDescriptionTmpdata"
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
          <br />
          <v-btn @click="downloadGeneratedImage" depressed color="primary"
            >ダウンロード</v-btn
          >
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
      cropedBinary: "",
      file: null,
      fileErrorMessages: [],
      sakeCategory: "",
      sakeName: "",
      sakeDescriptionTmpdata: "",
      sakeDescription: [],
      SAKE_INTRODUCTION_FIELD: {
        X_POS: 0,
        Y_POS: 0,
        WIDTH: 740,
        HEIGHT: 720,
      },
      CROPPED_DATA_FIELD: {
        X_POS: 740,
        Y_POS: 0,
        WIDTH: 540,
        HEIGHT: 720,
      },
    };
  },
  mounted() {
    // サンプル画像読み込み
    this.$nextTick(function () {
      this.imgSrc = this.$refs.examplesake.src;
    });
  },
  methods: {
    //酒の紹介を描画する
    drawIntroduction() {
      this.canvasContext = this.$refs.canvas.getContext("2d");
      // 文字領域を毎度削除する（上書きされない）
      this.canvasContext.clearRect(
        this.SAKE_INTRODUCTION_FIELD.X_POS,
        this.SAKE_INTRODUCTION_FIELD.Y_POS,
        this.SAKE_INTRODUCTION_FIELD.WIDTH,
        this.SAKE_INTRODUCTION_FIELD.HEIGHT
      );
      // フォントと位置指定
      this.canvasContext.font = "32px serif";
      this.canvasContext.fillStyle = "#404040";

      // 文字の書き込み
      this.canvasContext.fillText(this.sakeCategory, 100, 100);
      this.canvasContext.fillText(this.sakeName, 100, 200);
      this.wordWrap();
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
      this.cropedBinary = this.$refs.cropper.getCroppedCanvas().toDataURL();
      // cropした画像をImageのsrcとして設定
      var croppedData = new Image();
      croppedData.src = this.cropedBinary;
      // Canvasに画像を描画
      croppedData.onload = () => {
        var data = {
          x: Math.round(croppedData.x),
          y: Math.round(croppedData.y),
          width: Math.round(croppedData.width),
          height: Math.round(croppedData.height),
          vectorX: 1,
          vectorY: 1,
        };
        this.canvasContext = this.$refs.canvas.getContext("2d");
        this.canvasContext.drawImage(
          croppedData,
          data["x"],
          data["y"],
          data["width"],
          data["height"],
          740, //切り出されるCanvas内でのX座標指定
          0, //切り出されるCanvas内でのY座標指定
          data["vectorX"] * this.CROPPED_DATA_FIELD.WIDTH, //切り出される画像の横幅
          data["vectorY"] * this.CROPPED_DATA_FIELD.HEIGHT //切り出される画像の縦幅
        );
      };
    },
    wordWrap() {
      //入力文字を1文字毎に配列化
      var aryText = this.sakeDescriptionTmpdata.split("");

      //
      var SAKE_INTRODUCTION_CHAR_LENGTH = 15;
      var fontSize =
        this.SAKE_INTRODUCTION_FIELD.WIDTH / SAKE_INTRODUCTION_CHAR_LENGTH;

      //出力用の配列を用意
      this.sakeDescription = [];
      this.sakeDescription[0] = "";
      var row_cnt = 0;

      //入力1文字毎にループ改行コードもしくは折り返しで配列の添え字を足す
      for (var i = 0; i < aryText.length; i++) {
        var text = aryText[i];
        if (
          this.sakeDescription[row_cnt].length >= SAKE_INTRODUCTION_CHAR_LENGTH
        ) {
          row_cnt++;
          this.sakeDescription[row_cnt] = "";
        }
        if (text == "\n") {
          row_cnt++;
          this.sakeDescription[row_cnt] = "";
          text = "";
        }
        this.sakeDescription[row_cnt] += text;
      }
      //文字の表示y軸とx軸をループする
      for (var j = 0; j < this.sakeDescription.length; j++) {
        var aryStr = this.sakeDescription[j].split("");
        for (var k = 0; k < aryStr.length; k++) {
          this.canvasContext.fillText(
            aryStr[k],
            k * fontSize,
            j * fontSize + 300
          );
        }
      }
    },
    downloadGeneratedImage() {
      let canvas = this.$refs.canvas;
      let link = document.createElement("a");
      link.href = canvas.toDataURL("image/png");
      link.download = "test.png";
      link.click();
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
