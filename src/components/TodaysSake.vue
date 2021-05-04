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
            <div id="introduction" class="text-center">
              <div class="py-4">
                <span id="subtitle">これはなに</span>
                <p>
                  VRChatで飲み会などのイベントに参加している時に「何飲んでるの？」という話になることがあるかと思います。<br />
                  そんなとき、手持ち看板で自信満々に好きなお酒をアピールできるとカッコいいですよね。<br />
                  このサービスは、VRChatのアバターに飲んでいるお酒の情報を載せた看板を持たせる想定で、その看板のテクスチャをウェブ上で生成するサービスです。
                </p>
                <div id="exampleTwitter" class="text-center">
                  <Tweet
                    align="center"
                    width="250"
                    id="1385979508092653570"
                  ></Tweet>
                </div>
              </div>
              <div class="py-4">
                <span id="subtitle">どんなテクスチャがつくれるの？</span>
                <p>
                  <a target="_blank" href="https://booth.pm/ja/items/1085365"
                    >すずめはうす様がBoothで配布している「VRChat用立て看板」</a
                  >
                  のテクスチャに合成されたものを生成できます。<br />
                  そのため、ペイントツールなどを使うことなく、ウェブ上で作成したテクスチャをそのままモデルに適用することができます。
                  <br />
                  このサービスを利用して生成したテクスチャで、みなさんが好きなお酒を紹介している様子は
                  <a
                    target="_blank"
                    href="https://twitter.com/search?q=%23%E4%BB%8A%E3%81%AE%E9%85%92%E3%81%AF%E3%81%93%E3%82%8C&src=recent_search_click"
                    >#今の酒はこれ</a
                  >
                  でツイートしてくれるとうれしいです。
                </p>
              </div>
            </div>
            <div id="adjust" class="text-center">
              <h3>① まずは好きなお酒を入力しよう</h3>
              <div class="py-4">
                <img
                  alt="icon_beer"
                  src="../assets/icon_beer.png"
                  ref="iconbeer"
                  width="30"
                  height="30"
                  align="center"
                />
              </div>
              <p>
                看板に載せるお酒の情報と画像を入力します。<br />
                お酒の情報は、「お酒の種類」「お酒の名前」「お酒の説明」を入力する事ができます。「お酒の説明」については、改行を含めて看板に反映されます。<br />
                お酒の画像はアップロードして、任意の箇所をトリミングすることができます。
              </p>
            </div>
          </v-col>
          <v-col cols="6">
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
              <v-text-field
                v-model="sakePrice"
                label="お酒の価格"
                single-line
                full-width
                hide-details
                @input="drawIntroduction"
              ></v-text-field>
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
            <input
              style="display: none"
              ref="input"
              class="file-button"
              type="file"
              @change="upload"
            />
            <div class="py-2 text-center">
              <v-btn depressed color="primary" rounded @click="selectImageClick"
                >画像をアップロードする</v-btn
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
        <div id="adjust" class="text-center">
          <h3>② 入力した文字の位置やフォントを調整しよう</h3>
          <div class="py-4">
            <img
              alt="icon_beer"
              src="../assets/icon_beer.png"
              ref="iconbeer"
              width="30"
              height="30"
              align="center"
            />
          </div>
          <p>
            前段で入力した情報の文字位置を調整します。<br />
            入力した「お酒の種類」「お酒の名前」「お酒の説明」のそれぞれについて、「フォントサイズ」「縦の位置」「横の位置」を調整できます。<br />
            フォントについては共通で変更することができます。<br />
            <span class="red--text"
              >※現在、うまく反映されないバグがあります（何度か試行するとうまくいく場合があります）。</span
            >
          </p>
        </div>
        <v-row align="start" justify="center">
          <v-col cols="6">
            <v-row>
              <v-expansion-panels>
                <v-expansion-panel>
                  <v-expansion-panel-header>
                    お酒の種類
                  </v-expansion-panel-header>
                  <v-expansion-panel-content>
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
                          @input="drawIntroduction"
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
                          @input="drawIntroduction"
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
                          @input="drawIntroduction"
                        ></v-text-field>
                      </template>
                    </v-slider>
                  </v-expansion-panel-content>
                </v-expansion-panel>
                <v-expansion-panel>
                  <v-expansion-panel-header>
                    お酒の名前
                  </v-expansion-panel-header>
                  <v-expansion-panel-content>
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
                          @input="drawIntroduction"
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
                          @input="drawIntroduction"
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
                          @input="drawIntroduction"
                        ></v-text-field>
                      </template>
                    </v-slider>
                  </v-expansion-panel-content>
                </v-expansion-panel>
                <v-expansion-panel>
                  <v-expansion-panel-header>
                    お酒の価格
                  </v-expansion-panel-header>
                  <v-expansion-panel-content>
                    <v-subheader>フォントサイズ</v-subheader>
                    <v-slider
                      v-model="sakePriceFontSize"
                      class="align-center"
                      :max="100"
                      :min="5"
                      hide-details
                      @change="drawIntroduction"
                    >
                      <template v-slot:append>
                        <v-text-field
                          v-model="sakePriceFontSize"
                          class="mt-0 pt-0"
                          hide-details
                          single-line
                          type="number"
                          style="width: 60px"
                          @input="drawIntroduction"
                        ></v-text-field>
                      </template>
                    </v-slider>
                    <v-subheader>縦の位置</v-subheader>
                    <v-slider
                      v-model="sakePriceYPos"
                      class="align-center"
                      :max="720"
                      :min="0"
                      hide-details
                      @change="drawIntroduction"
                    >
                      <template v-slot:append>
                        <v-text-field
                          v-model="sakePriceYPos"
                          class="mt-0 pt-0"
                          hide-details
                          single-line
                          type="number"
                          style="width: 60px"
                          @input="drawIntroduction"
                        ></v-text-field>
                      </template>
                    </v-slider>
                    <v-subheader>横の位置</v-subheader>
                    <v-slider
                      v-model="sakePriceXPos"
                      class="align-center"
                      :max="200"
                      :min="0"
                      hide-details
                      @change="drawIntroduction"
                    >
                      <template v-slot:append>
                        <v-text-field
                          v-model="sakePriceXPos"
                          class="mt-0 pt-0"
                          hide-details
                          single-line
                          type="number"
                          style="width: 60px"
                          @input="drawIntroduction"
                        ></v-text-field>
                      </template>
                    </v-slider>
                  </v-expansion-panel-content>
                </v-expansion-panel>
                <v-expansion-panel>
                  <v-expansion-panel-header>
                    お酒の説明
                  </v-expansion-panel-header>
                  <v-expansion-panel-content>
                    <h4 class="text-center"></h4>
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
                          @input="drawIntroduction"
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
                          @input="drawIntroduction"
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
                          @input="drawIntroduction"
                        ></v-text-field>
                      </template>
                    </v-slider>
                  </v-expansion-panel-content>
                </v-expansion-panel>
              </v-expansion-panels>
              <div class="py-4">
                <v-select
                  v-model="fontTypeCurrent"
                  :items="fontTypes"
                  label="フォント"
                  @change="drawIntroduction"
                ></v-select>
                <v-color-picker
                  dot-size="25"
                  hide-mode-switch
                  hide-inputs
                  v-model="fontColorCurrent"
                ></v-color-picker>
              </div>
            </v-row>
          </v-col>
          <v-col cols="6">
            <!-- 合成画像領域（Canvas） -->
            <canvas
              id="canvas"
              width="1280"
              height="720"
              ref="generatedImageCanvas"
              style="width: 800px"
            ></canvas>
          </v-col>
          <div id="adjust" class="text-center">
            <h3>③ ダウンロードしてアバターに設定しよう</h3>
            <div class="py-4">
              <img
                alt="icon_beer"
                src="../assets/icon_beer.png"
                ref="iconbeer"
                width="30"
                height="30"
                align="center"
              />
            </div>
            <p>
              前段で調整した画像をダウンロードしてアバターに設定します。<br />
              プレビュー画面に表示されている画像のみをダウンロードすることもできますし、看板のテクスチャに合成された状態のものをダウンロードすることもできます。<br />
              ダウンロードした画像をモデルに貼り付けるなど、Unityでアバターに設定して遊びましょう！<br />
              <br />
            </p>
          </div>
          <v-row align="start" justify="center">
            <img
              alt="unity_screenshot"
              src="../assets/unity_screenshot.jpg"
              ref="unityScreenshot"
              style="width: 600px"
            />
            <v-col cols="12">
              <div class="pa-2 text-center">
                <v-btn @click="downloadGeneratedImage" depressed color="primary"
                  >ダウンロード</v-btn
                ><br />
                プレビュー画面に表示されている画像をダウンロードします。
              </div>
              <div class="pa-2 text-center">
                <v-btn
                  @click="downloadGeneratedImageWithTexture"
                  depressed
                  color="primary"
                  >ダウンロード（テクスチャ合成済み）</v-btn
                ><br />
                <a target="_blank" href="https://booth.pm/ja/items/1085365"
                  >すずめはうす様がBoothで配布している「VRChat用立て看板」</a
                >
                に同梱されているテクスチャに合成された画像をダウンロードします。<br />
              </div>
              <div id="enjoy" class="pt-4 text-center">
                <p>Let's enjoy drinking!</p>
              </div>
              <p class="text-center">
                <a
                  target="_blank"
                  href="https://twitter.com/search?q=%23%E4%BB%8A%E3%81%AE%E9%85%92%E3%81%AF%E3%81%93%E3%82%8C&src=recent_search_click"
                  >#今の酒はこれ</a
                >
                でみなさんの好きなお酒を紹介してくれるとうれしいです。
              </p>
              <div id="thanks" class="py-8 text-left">
                <p>
                  「VRChat用立て看板」のテクスチャ利用を許可してくださった、すずめい子（@suzumeiko_as）様、ありがとうございました。
                </p>
              </div>
            </v-col>
          </v-row>
          <!-- 非表示テクスチャキャンバス -->
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
import { Tweet } from "vue-tweet-embed";
import WebFontLoader from "webfontloader";

export default {
  name: "TodaysSake",
  components: {
    VueCropper,
    Tweet,
  },
  props: {
    value: {
      type: String,
      default: null,
    },
  },
  watch: {
    // フォント色がピッカーで変更された時に再描画させる
    fontColorCurrent: "drawIntroduction",
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
      fontColorCurrent: "#404040",
      sakeCategory: "チューハイ・カクテル",
      sakeCategoryFontSize: 30,
      sakeCategoryYPos: 135,
      sakeCategoryXPos: 50,
      sakeName: "麒麟　発酵レモンサワー",
      sakeNameFontSize: 50,
      sakeNameYPos: 235,
      sakeNameXPos: 50,
      sakePrice: "207円 / 500ml",
      sakePriceFontSize: 30,
      sakePriceYPos: 285,
      sakePriceXPos: 50,
      sakeDescriptionFontSize: 30,
      sakeDescriptionYPos: 380,
      sakeDescriptionXPos: 50,
      sakeDescriptionTmpdata:
        "レモン果汁を酵母発酵させてるらしい。\n作り物の酸っぱいだけというか\nそういう感じがあんまりしない。\n\n「爽やか」ってラベルに書いてるけど納得。\nサッポロの「こだわり酒場のレモンサワー」\nよりも好きかも知れんね。",
      sakeDescription: [],
      hashTag: "＃今の酒はこれ",
      hashTagFontSize: "bold 35",
      hashTagYPos: 650,
      hashTagXPos: 50,
      hashtagFontType: "Kiwi Maru",
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
  created() {
    WebFontLoader.load({
      google: {
        // Use FVD notation to include families https://github.com/typekit/fvd
        families: [
          "Kiwi+Maru:400,500",
          "M+PLUS+1p",
          "M+PLUS+Rounded+1c",
          "New+Tegomin",
          "Noto+Sans+JP",
          "Potta+One",
          "Yusei+Magic",
        ],
      },
      active: function () {
        sessionStorage.fonts = true;
      },
    });
  },
  mounted() {
    // プレビュー画面に線を引く
    this.canvasContext = this.$refs.generatedImageCanvas.getContext("2d");
    this.canvasContext.strokeStyle = "#999999";
    this.canvasContext.strokeRect(0, 0, 1280, 720);
    this.drawIntroduction();
    // サンプル画像読み込み
    this.$nextTick(function () {
      this.imgSrc = this.$refs.examplesake.src;
      this.cropImage();
    });
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

      // 文字をそれぞれ描画する
      this.drawSakeCategory();
      this.drawSakeName();
      this.drawSakePrice();
      this.drawSakeDescription();
      this.drawHashTag();
    },
    // input属性のメソッド実行
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
      // cropした画像をImageのsrcとして設定
      var croppedData = new Image();
      if (this.$refs.cropper != undefined || this.$refs.cropper != null) {
        // cropした画像を保管
        this.cropedBinary = this.$refs.cropper.getCroppedCanvas().toDataURL();
        croppedData.src = this.cropedBinary;
      } else {
        croppedData.src = this.$refs.examplesake.src;
      }
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
      this.canvasContext.fillStyle = this.fontColorCurrent;
      this.canvasContext.fillText(
        this.sakeCategory,
        this.sakeCategoryXPos,
        this.sakeCategoryYPos
      );
    },
    drawSakePrice() {
      // 文字の書き込み
      this.canvasContext.font =
        this.sakePriceFontSize + "px " + this.fontTypeCurrent;
      this.canvasContext.fillStyle = this.fontColorCurrent;
      this.canvasContext.fillText(
        this.sakePrice,
        this.sakePriceXPos,
        this.sakePriceYPos
      );
    },
    drawSakeCategory() {
      this.canvasContext.font =
        this.sakeNameFontSize + "px " + this.fontTypeCurrent;
      this.canvasContext.fillStyle = this.fontColorCurrent;
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
          this.canvasContext.fillStyle = this.fontColorCurrent;
          this.canvasContext.fillText(
            aryStr[k],
            k * this.sakeDescriptionFontSize + this.sakeDescriptionXPos,
            j * this.sakeDescriptionFontSize + this.sakeDescriptionYPos
          );
        }
      }
    },
    drawHashTag() {
      this.canvasContext.font =
        this.hashTagFontSize + "px " + this.hashtagFontType;
      this.canvasContext.fillStyle = this.fontColorCurrent;
      this.canvasContext.fillText(
        this.hashTag,
        this.hashTagXPos,
        this.hashTagYPos
      );
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
      const ovarlayImage = await this.getImagefromCanvas();
      canvasContext.drawImage(
        ovarlayImage,
        0,
        0,
        ovarlayImage.width,
        ovarlayImage.height,
        1004,
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
  margin: 40px 0 20px 0;
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
  font-size: 1.5rem;
}
#subtitle {
  font-size: 1.5rem;
}
#exampleTwitter {
  text-align: center;
  margin-left: auto;
  margin-right: auto;
}
#enjoy {
  margin: 40px 0 0 0;
  font-size: 3rem;
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
