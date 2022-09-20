<template>
  <v-main>
    <v-row justify="center" align="center" style="height: 90vh">
      <v-col cols="auto">
        <v-card flat style="background-color: inherit">
          <v-card-title
            class="justify-center my-4"
            style="font-size: 53px; font-weight: bold; color: #3b4043"
          >
            会話デッキ（仮）
          </v-card-title>
          <v-card-text class="mt-8" style="padding;: 16px; color: #3b4043">
            <p style="font-size: 16px; text-align: center">
              あー、ちゃんと話すこと準備しとけばよかったなぁってことありませんか。<br />
              これはそんな⽤意周到なあなたのために作成されたWebアプリです。<br />
              あなたの⽇常をより豊かになることをお祈りしております。<br />
            </p>
          </v-card-text>
          <v-text-field
            placeholder="デッキコード(12桁)"
            v-model="deckCode"
            solo
            dense
            outlined
            flat
            clearable
            style="width: 340px; margin: auto"
          ></v-text-field>
          <v-card-actions style="justify-content: center">
            <v-btn
              class="mx-5"
              style="
                width: 180px;
                height: 60px;
                font-weight: bold;
                font-size: 20px;
                color: white;
              "
              depressed
              color="#004BB1"
              @click="duelStart()"
            >
              デュエル開始
            </v-btn>
            <v-btn
              class="mx-5"
              style="
                width: 180px;
                height: 60px;
                font-weight: bold;
                font-size: 20px;
                color: #004bb1;
              "
              depressed
              outlined
              @click="deckEdit()"
            >
              デッキ編集
            </v-btn>
          </v-card-actions>
        </v-card>
      </v-col>
    </v-row>
  </v-main>
</template>

<script>
export default {
  head() {
    return {
      title: "ホーム",
    };
  },
  data() {
    return {
      deckCode: null,
    };
  },
  methods: {
    duelStart() {
      switch (this.checkDeckCode()) {
        case "NotEntered":
          alert("デュエルを開始するにはデッキコードが必須です。");
          break;
        case "Exist":
          this.$router.push(`/topic_deck/duel/${this.deckCode}`);
          break;
        case "NotExist":
          alert("存在しないデッキコードです。");
          break;
      }
    },
    deckEdit() {
      switch (this.checkDeckCode()) {
        case "NotEntered":
          // 存在しないデッキコードを生成
          const chars =
            "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789";
          let newDeckCode = "";
          while (newDeckCode === "") {
            for (let i = 0; i < 12; i++) {
              newDeckCode += chars.charAt(
                Math.floor(Math.random() * chars.length)
              );
            }
          }
          this.$router.push(`topic_deck/deck_edit/${newDeckCode}`);
          break;
        case "Exist":
          this.$router.push(`topic_deck/deck_edit/${this.deckCode}`);
          break;
        case "NotExist":
          alert("存在しないデッキコードです。");
          break;
      }
    },
    // TODO: enum使いてぇ。
    checkDeckCode() {
      let result = "";
      // 入力チェック
      if (this.deckCode === null) {
        result = "NotEntered";
      }
      // 存在チェック
      // else if(){
      //   result = 'Exist'
      // }
      // それ以外
      else {
        result = "NotExist";
      }

      return result;
    },
  },
};
</script>

<style></style>
