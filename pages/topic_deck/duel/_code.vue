<template>
  <div>
    <v-app-bar style="position: fixed" app>
      <v-container>
        <v-row>
          <v-col cols="auto" style="margin: auto">
            <div style="font-size: 24px">
              {{ deckName }}
            </div>
          </v-col>
          <v-col cols="auto">
            <v-divider vertical></v-divider>
          </v-col>
          <v-col cols="auto">
            <v-tooltip bottom>
              <template v-slot:activator="{ on }">
                <v-btn icon v-on="on" @click="deckCodeCopyToClipboard">
                  <v-icon>mdi-content-copy</v-icon>
                </v-btn>
              </template>
              <span>デッキコードをコピー</span>
            </v-tooltip>
          </v-col>
          <v-col> </v-col>
          <v-col cols="auto">
            <v-btn
              class="mt-1"
              style="
                border-radius: 20px;
                font-weight: bold;
                font-size: 16px;
                color: white;
              "
              color="#004BB1"
              depressed
              @click="draw()"
              v-if="!isEmptyCardList"
            >
              ドロー
            </v-btn>
            <v-btn
              class="mt-1"
              style="
                border-radius: 20px;
                font-weight: bold;
                font-size: 16px;
                color: white;
              "
              color="#004BB1"
              depressed
              @click="resetCardList()"
              v-else
            >
              やり直す
            </v-btn>
          </v-col>
          <v-col cols="auto">
            <v-btn
              class="mt-1"
              style="
                border-radius: 20px;
                font-weight: bold;
                font-size: 16px;
                color: white;
              "
              color="#004BB1"
              depressed
              @click="toEdit()"
            >
              デッキ編集へ
            </v-btn>
          </v-col>
          <v-col cols="auto">
            <v-tooltip bottom>
              <template v-slot:activator="{ on }">
                <v-btn icon v-on="on">
                  <v-icon>mdi-twitter</v-icon>
                </v-btn>
              </template>
              <span>未実装です...(=</span>
            </v-tooltip>
          </v-col>
        </v-row>
      </v-container>
    </v-app-bar>
    <v-main class="mt-5">
      <v-container>
        <v-row v-for="item in drawList" v-bind:key="item.index">
          <v-col>
            <v-card style="display: flex">
              <v-card-text style="max-width: 100%; white-space: pre-line"
                >{{ item.context }} : {{ item.order }}
              </v-card-text>
            </v-card>
          </v-col>
        </v-row>
      </v-container>
    </v-main>
  </div>
</template>

<script>
export default {
  head() {
    return {
      title: "デュエル",
    };
  },
  data() {
    return {
      deckName: "新規デッキ",
      isEmptyCardList: false,
      cardList: [],
      bkCardList: [],
      drawList: [],
    };
  },
  mounted() {
    for (let index = 0; index < 10; index++) {
      let a = {
        order: index,
        context: `サンプル${index}\nこれはサンプルっす。`,
        bkContext: "",
        edit: false,
      };
      this.cardList.push(a);
    }
    this.bkCardList = this.cardList.concat();
  },
  methods: {
    draw() {
      if (this.cardList.length > 0) {
        let a = this.cardList[Math.floor(Math.random() * this.cardList.length)];
        this.drawList.push(a);
        this.cardListDelete(a);
        console.log(this.cardList);
      } else {
        alert("手札がつきました");
        this.isEmptyCardList = true;
      }
    },
    resetCardList() {
      this.cardList = this.drawList;
      this.drawList = [];
      this.isEmptyCardList = false;
    },
    cardListDelete(item) {
      this.cardList = this.cardList.filter((v) => {
        return v != item;
      });
      this.cardList.forEach((v, index) => {
        v.order = index;
      });
      this.isEditDeck = true;
    },
    toEdit() {
      alert("デッキ編集へ移動します。");
      this.$router.push(`/topic_deck/deck_edit/${this.$route.params.code}`);
    },
    deckCodeCopyToClipboard() {
      navigator.clipboard.writeText(this.$route.params.code);
    },
  },
};
</script>

<style></style>
