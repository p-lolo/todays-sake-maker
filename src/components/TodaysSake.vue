<template>
  <v-container>
    <div id="body">
      <div id="making">
        <v-row align="start" justify="space-around">
          <v-col cols="12">
            <h2 id="title" class="h2 text-center">
              今の酒はこれ！ジェネレーター
            </h2>
            <br />
            <p class="text-center">
              <span id="subtitle"
                ><b>いま飲んでいるお酒をVRChatでアピールしよう！</b></span
              >
            </p>
            <div class="py-4">
              <img
                alt="icon_beer"
                src="../assets/icon_beer.png"
                ref="iconbeer"
                width="100"
                height="100"
                align="left"
              />
              <span id="subtitle">これはなに</span>
              <p>
                VRChatで飲み会などのイベントに参加している時に、「何飲んでるの？」と聞かれることがあるかと思います。<br />
                そんなとき、手持ち看板で自信満々に好きなお酒をアピールしたくなりますよね。<br />
                このサービスはVRChatのアバターに飲んでいるお酒の情報を載せた看板のテクスチャをウェブ上で生成するサービスです。
              </p>
            </div>
            <div class="py-4">
              <img
                alt="icon_nihonshu"
                src="../assets/icon_nihonshu.png"
                ref="iconnihonshu"
                width="100"
                height="100"
                align="left"
              />
              <span id="subtitle">どんなテクスチャがつくれるの？</span>
              <p>
                <a target="_blank" href="https://booth.pm/ja/items/1085365"
                  >すずめはうす様がBoothで配布しているVRChat用立て看板</a
                >
                のテクスチャに合成されたものが生成されます。<br />
                そのため、ウェブ上で作成したテクスチャを、ペイントツールなどを噛ますことなくそのまま利用することができます。
                <br />
                このサービスでテクスチャを作ってVRChatでアピールしている様子は
                <a
                  target="_blank"
                  href="https://twitter.com/search?q=%23%E4%BB%8A%E3%81%AE%E9%85%92%E3%81%AF%E3%81%93%E3%82%8C&src=recent_search_click"
                  >#今の酒はこれ</a
                >
                でツイートしてくれると嬉しいです。
              </p>
            </div>
            <div id="adjust">
              <h3 class="text-center">① まずは好きなお酒を入力しよう</h3>
            </div>
          </v-col>
          <v-col cols="6">
            <h4 class="text-center">お酒の情報</h4>
            <v-form>
              <v-text-field
                v-model="sakeCategory"
                label="お酒の種類"
                single-line
                full-width
                hide-details
                @input="drawIntroduction"
              ></v-text-field>
              <v-text-field
                v-model="sakeName"
                label="お酒の名前"
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
            <h4 class="text-center">お酒の画像</h4>
            <input
              style="display: none"
              ref="input"
              class="file-button"
              type="file"
              @change="upload"
            />
            <div class="py-2">
              <v-btn depressed color="primary" rounded @click="selectImageClick"
                >画像アップロード</v-btn
              >
              <ul v-if="fileErrorMessages.length > 0" class="error-messages">
                <li v-for="(message, index) in fileErrorMessages" :key="index">
                  {{ message }}
                </li>
              </ul>
            </div>
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
                :img-style="{ width: '400', height: '400px' }"
              />
            </div>
            <div class="py-2">
              <v-btn
                div
                color="primary"
                type="button"
                depressed
                block
                @click="cropImage"
                >トリミングする</v-btn
              >
            </div>
          </v-col>
        </v-row>
        <div id="adjust">
          <h3 class="text-center">
            ② 入力した文字の位置やフォントを調整しよう
          </h3>
        </div>
        <v-row align="start" justify="center">
          <v-col cols="6">
            <v-row>
              <v-col cols="4">
                <h4 class="text-center">お酒の種類</h4>
                <v-subheader>フォントサイズ</v-subheader>
                <v-slider
                  v-model="sakeCategoryFontSize"
                  class="align-center"
                  :max="50"
                  :min="5"
                  hide-details
                  @change="drawIntroduction"
                >
                  <template v-slot:append>
                    <v-text-field
                      v-model="sakeCategoryFontSize"
                      class="mt-0 pt-0"
                      hide-details
                      single-line
                      type="number"
                      style="width: 60px"
                      @change="drawIntroduction"
                    ></v-text-field>
                  </template>
                </v-slider>
                <v-subheader>縦の位置</v-subheader>
                <v-slider
                  v-model="sakeCategoryYPos"
                  class="align-center"
                  :max="720"
                  :min="0"
                  hide-details
                  @change="drawIntroduction"
                >
                  <template v-slot:append>
                    <v-text-field
                      v-model="sakeCategoryYPos"
                      class="mt-0 pt-0"
                      hide-details
                      single-line
                      type="number"
                      style="width: 60px"
                      @change="drawIntroduction"
                    ></v-text-field>
                  </template>
                </v-slider>
                <v-subheader>横の位置</v-subheader>
                <v-slider
                  v-model="sakeCategoryXPos"
                  class="align-center"
                  :max="200"
                  :min="0"
                  hide-details
                  @change="drawIntroduction"
                >
                  <template v-slot:append>
                    <v-text-field
                      v-model="sakeCategoryXPos"
                      class="mt-0 pt-0"
                      hide-details
                      single-line
                      type="number"
                      style="width: 60px"
                      @change="drawIntroduction"
                    ></v-text-field>
                  </template>
                </v-slider>
              </v-col>
              <v-col cols="4">
                <h4 class="text-center">お酒の名前</h4>
                <v-subheader>フォントサイズ</v-subheader>
                <v-slider
                  v-model="sakeNameFontSize"
                  class="align-center"
                  :max="100"
                  :min="5"
                  hide-details
                  @change="drawIntroduction"
                >
                  <template v-slot:append>
                    <v-text-field
                      v-model="sakeNameFontSize"
                      class="mt-0 pt-0"
                      hide-details
                      single-line
                      type="number"
                      style="width: 60px"
                      @change="drawIntroduction"
                    ></v-text-field>
                  </template>
                </v-slider>
                <v-subheader>縦の位置</v-subheader>
                <v-slider
                  v-model="sakeNameYPos"
                  class="align-center"
                  :max="720"
                  :min="0"
                  hide-details
                  @change="drawIntroduction"
                >
                  <template v-slot:append>
                    <v-text-field
                      v-model="sakeNameYPos"
                      class="mt-0 pt-0"
                      hide-details
                      single-line
                      type="number"
                      style="width: 60px"
                      @change="drawIntroduction"
                    ></v-text-field>
                  </template>
                </v-slider>
                <v-subheader>横の位置</v-subheader>
                <v-slider
                  v-model="sakeNameXPos"
                  class="align-center"
                  :max="200"
                  :min="0"
                  hide-details
                  @change="drawIntroduction"
                >
                  <template v-slot:append>
                    <v-text-field
                      v-model="sakeNameXPos"
                      class="mt-0 pt-0"
                      hide-details
                      single-line
                      type="number"
                      style="width: 60px"
                      @change="drawIntroduction"
                    ></v-text-field>
                  </template>
                </v-slider>
              </v-col>
              <v-col cols="4">
                <h4 class="text-center">お酒の説明</h4>
                <v-subheader>フォントサイズ</v-subheader>
                <v-slider
                  v-model="sakeDescriptionFontSize"
                  class="align-center"
                  :max="50"
                  :min="5"
                  hide-details
                  @change="drawIntroduction"
                >
                  <template v-slot:append>
                    <v-text-field
                      v-model="sakeDescriptionFontSize"
                      class="mt-0 pt-0"
                      hide-details
                      single-line
                      type="number"
                      style="width: 60px"
                      @change="drawIntroduction"
                    ></v-text-field>
                  </template>
                </v-slider>
                <v-subheader>縦の位置</v-subheader>
                <v-slider
                  v-model="sakeDescriptionYPos"
                  class="align-center"
                  :max="720"
                  :min="0"
                  hide-details
                  @change="drawIntroduction"
                >
                  <template v-slot:append>
                    <v-text-field
                      v-model="sakeDescriptionYPos"
                      class="mt-0 pt-0"
                      hide-details
                      single-line
                      type="number"
                      style="width: 60px"
                      @change="drawIntroduction"
                    ></v-text-field>
                  </template>
                </v-slider>
                <v-subheader>横の位置</v-subheader>
                <v-slider
                  v-model="sakeDescriptionXPos"
                  class="align-center"
                  :max="200"
                  :min="0"
                  hide-details
                  @change="drawIntroduction"
                >
                  <template v-slot:append>
                    <v-text-field
                      v-model="sakeDescriptionXPos"
                      class="mt-0 pt-0"
                      hide-details
                      single-line
                      type="number"
                      style="width: 60px"
                      @change="drawIntroduction"
                    ></v-text-field>
                  </template>
                </v-slider>
              </v-col>
            </v-row>
            <v-select
              v-model="fontTypeCurrent"
              :items="fontTypes"
              label="フォント"
              @change="drawIntroduction"
            ></v-select>
          </v-col>
          <v-col cols="6">
            <h4 class="text-center">プレビュー</h4>
            <!-- 合成画像領域（Canvas） -->
            <canvas
              id="canvas"
              width="1280"
              height="720"
              ref="generatedImageCanvas"
              style="width: 800px"
            ></canvas>
          </v-col>
          <div id="adjust">
            <h3 class="text-center">③ ダウンロードしてアバターに設定しよう</h3>
          </div>
          <v-row align="start" justify="center">
            <v-col cols="12">
              <div class="pa-2">
                <v-btn @click="downloadGeneratedImage" depressed color="primary"
                  >ダウンロード</v-btn
                >
                プレビュー画面に表示されているもののみをダウンロードします
              </div>
              <div class="pa-2">
                <v-btn
                  @click="downloadGeneratedImageWithTexture"
                  depressed
                  color="primary"
                  >ダウンロード（テクスチャ合成済み）</v-btn
                >
                プレビュー画面に表示されているもののみをダウンロードします
              </div>
            </v-col>
          </v-row>
          <canvas
            style="display: none"
            id="canvas"
            width="2048"
            height="1024"
            ref="textureCanvas"
          ></canvas>
          <!-- 非表示サンプル酒画像 -->
          <img
            alt="example_sake"
            src="../assets/example_sake.jpg"
            style="display: none"
            ref="examplesake"
          />
          <img
            alt="board_texture"
            src="../assets/board_texture.png"
            style="display: none"
            ref="boardtexture"
          />
        </v-row>
      </div>
    </div>
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
      cropedBinary: "",
      file: null,
      fileErrorMessages: [],
      fontTypes: [
        "メイリオ",
        "ＭＳ ゴシック",
        "游ゴシック",
        "HG明朝E",
        "ＭＳ 明朝",
        "ヒラギノ角ゴ Pro W3",
        "Kiwi Maru",
        "M PLUS 1p",
        "M PLUS Rounded 1c",
        "New Tegomin",
        "Noto Sans JP",
        "Potta One",
        "Yusei Magic",
      ],
      fontTypeCurrent: "HG明朝E",
      sakeCategory: "チューハイ・カクテル",
      sakeCategoryFontSize: 30,
      sakeCategoryYPos: 135,
      sakeCategoryXPos: 50,
      sakeName: "麒麟　発酵レモンサワー",
      sakeNameFontSize: 50,
      sakeNameYPos: 235,
      sakeNameXPos: 50,
      sakeDescriptionFontSize: 30,
      sakeDescriptionYPos: 380,
      sakeDescriptionXPos: 50,
      sakeDescriptionTmpdata:
        "レモン果汁を酵母発酵させてるらしい。\n作り物の酸っぱいだけというか\nそういう感じがあんまりしない。\n\n「爽やか」ってラベルに書いてるけど納得。\nサッポロの「こだわり酒場のレモンサワー」\nよりも好きかも知れんね。",
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
      this.cropImage();
    });
    // プレビュー画面に線を引く
    this.canvasContext = this.$refs.generatedImageCanvas.getContext("2d");
    this.canvasContext.strokeStyle = "#999999";
    this.canvasContext.strokeRect(0, 0, 1280, 720);
    this.drawIntroduction();
  },
  methods: {
    //酒の紹介を描画する
    drawIntroduction() {
      this.canvasContext = this.$refs.generatedImageCanvas.getContext("2d");
      // 文字領域を毎度削除する（上書きされない）
      this.canvasContext.clearRect(
        this.SAKE_INTRODUCTION_FIELD.X_POS,
        this.SAKE_INTRODUCTION_FIELD.Y_POS,
        this.SAKE_INTRODUCTION_FIELD.WIDTH,
        this.SAKE_INTRODUCTION_FIELD.HEIGHT
      );
      // 文字領域で消された枠線を引き直す
      this.canvasContext.strokeStyle = "#999999";
      this.canvasContext.strokeRect(0, 0, 1280, 720);

      this.drawSakeCategory();
      this.drawSakeName();
      this.drawSakeDescription();
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
          "アップロードできるのは、jpg画像ファイルかpng画像ファイルのみです。"
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
        this.canvasContext = this.$refs.generatedImageCanvas.getContext("2d");
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
    drawSakeName() {
      // 文字の書き込み
      this.canvasContext.font =
        this.sakeCategoryFontSize + "px " + this.fontTypeCurrent;
      this.canvasContext.fillStyle = "#404040";
      this.canvasContext.fillText(
        this.sakeCategory,
        this.sakeCategoryXPos,
        this.sakeCategoryYPos
      );
    },
    drawSakeCategory() {
      this.canvasContext.font =
        this.sakeNameFontSize + "px " + this.fontTypeCurrent;
      this.canvasContext.fillStyle = "#404040";
      this.canvasContext.fillText(
        this.sakeName,
        this.sakeNameXPos,
        this.sakeNameYPos
      );
    },
    drawSakeDescription() {
      //入力文字を1文字毎に配列化
      var aryText = this.sakeDescriptionTmpdata.split("");

      var charLength =
        this.SAKE_INTRODUCTION_FIELD.WIDTH - 100 / this.sakeDescriptionFontSize;

      //出力用の配列を用意
      this.sakeDescription = [];
      this.sakeDescription[0] = "";
      var rowCnt = 0;

      //入力1文字毎にループ改行コードもしくは折り返しで配列の添え字を足す
      for (var i = 0; i < aryText.length; i++) {
        var text = aryText[i];
        if (this.sakeDescription[rowCnt].length >= charLength) {
          rowCnt++;
          this.sakeDescription[rowCnt] = "";
        }
        if (text == "\n") {
          rowCnt++;
          this.sakeDescription[rowCnt] = "";
          text = "";
        }
        this.sakeDescription[rowCnt] += text;
      }
      //文字の表示y軸とx軸をループする
      for (var j = 0; j < this.sakeDescription.length; j++) {
        var aryStr = this.sakeDescription[j].split("");
        for (var k = 0; k < aryStr.length; k++) {
          this.canvasContext.font =
            this.sakeDescriptionFontSize + "px " + this.fontTypeCurrent;
          this.canvasContext.fillStyle = "#404040";
          this.canvasContext.fillText(
            aryStr[k],
            k * this.sakeDescriptionFontSize + this.sakeDescriptionXPos,
            j * this.sakeDescriptionFontSize + this.sakeDescriptionYPos
          );
        }
      }
    },
    downloadGeneratedImage() {
      let canvas = this.$refs.generatedImageCanvas;
      let link = document.createElement("a");
      link.href = canvas.toDataURL("image/png");
      link.download = "test.png";
      link.click();
    },
    async downloadGeneratedImageWithTexture() {
      var canvasContext = this.$refs.textureCanvas.getContext("2d");
      // ベースのテクスチャの描画
      var image = new Image();
      image.src = this.$refs.boardtexture.src;
      canvasContext.drawImage(image, 0, 0);
      // 生成したテクスチャの描画
      const image2 = await this.getImagefromCanvas();
      console.log(image2);
      canvasContext.drawImage(
        image2,
        2,
        2,
        image2.width-2,
        image2.height-2,
        1014,
        495,
        914,
        515
      );
      // ダウンロード
      let link = document.createElement("a");
      link.href = canvasContext.canvas.toDataURL("image/png");
      link.download = "test.png";
      link.click();
    },
    getImagefromCanvas() {
      return new Promise((resolve, reject) => {
        const image = new Image();
        const ctx = this.$refs.generatedImageCanvas.getContext("2d");
        image.onload = () => resolve(image);
        image.onerror = (e) => reject(e);
        image.src = ctx.canvas.toDataURL();
      });
    },
  },
};
</script>

<style scoped>
#adjust {
  margin: 50px 0 20px 0;
}
#preview {
  margin: 0 0 0 0;
}
#body {
  margin: 0 150px 0 150px;
}
h1 {
  font-size: 6rem;
  margin: 20px 0 0;
}
h2 {
  font-size: 3.75rem;
  margin: 20px 0 0;
}
h3 {
  font-size: 3rem;
  margin: 20px 0 0;
}
h4 {
  font-size: 2.125rem;
}
#subtitle {
  font-size: 1.5rem;
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
