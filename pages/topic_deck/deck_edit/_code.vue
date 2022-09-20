<template>
  <div>
    <v-app-bar style="position: fixed" app>
      <v-container>
        <v-row>
          <v-col cols="auto" style="margin: auto">
            <div style="font-size: 24px" v-if="!isEditDeckName">
              {{ deckName }}
            </div>
            <!-- 位置がめちゃめちゃ気になるけど沼ったのでスルー -->
            <v-text-field
              v-else
              append-icon="mdi-pencil"
              v-model="deckName"
              style="font-size: 24px"
              autofocus
              @keydown.enter.exact="isDeckNameEdit"
            >
            </v-text-field>
          </v-col>
          <v-col cols="auto">
            <v-divider vertical></v-divider>
          </v-col>
          <v-col cols="auto">
            <v-btn icon @click="isDeckNameEdit">
              <v-icon>mdi-square-edit-outline</v-icon>
            </v-btn>
          </v-col>
          <v-col cols="auto">
            <v-tooltip bottom>
              <template v-slot:activator="{ on }">
                <v-btn icon v-on="on">
                  <v-icon>mdi-content-copy</v-icon>
                </v-btn>
              </template>
              <span>デッキコードをコピー</span>
            </v-tooltip>
          </v-col>
          <v-col> </v-col>
          <v-col cols="auto" style="margin-left: auto">
            <v-btn icon @click="cardListAdd()">
              <v-icon>mdi-plus-circle</v-icon>
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
              :disabled="!isEditDeck"
              :loading="isSaving"
              @click="deckSave()"
            >
              保存
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
              :disabled="isSaving"
              @click="toDuel()"
            >
              デュエルへ
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
        <draggable
          v-model="cardList"
          :options="{ handle: '.handle' }"
          @update="listUpdate"
        >
          <v-row v-for="item in cardList" v-bind:key="item.index">
            <v-col>
              <v-card style="display: flex">
                <v-icon
                  class="ml-2"
                  v-bind:class="{
                    handle: item.edit ? false : true,
                  }"
                >
                  mdi-drag-vertical-variant</v-icon
                >
                <template v-if="!item.edit">
                  <v-card-text style="max-width: 100%; white-space: pre-line"
                    >{{ item.context }} : {{ item.order }}
                  </v-card-text>
                  <v-card-actions style="margin-left: auto">
                    <v-tooltip bottom>
                      <template v-slot:activator="{ on }">
                        <v-btn icon v-on="on" @click="isCardDuplication(item)">
                          <v-icon>mdi-content-copy</v-icon>
                        </v-btn>
                      </template>
                      <span>カードを複製</span>
                    </v-tooltip>
                  </v-card-actions>
                  <v-card-actions>
                    <v-tooltip bottom>
                      <template v-slot:activator="{ on }">
                        <v-btn icon v-on="on" @click="isCardEdit(item)">
                          <v-icon>mdi-square-edit-outline</v-icon>
                        </v-btn>
                      </template>
                      <span>カードを編集</span>
                    </v-tooltip>
                  </v-card-actions>
                  <v-card-actions>
                    <v-tooltip bottom>
                      <template v-slot:activator="{ on }">
                        <v-btn icon v-on="on" @click="cardListDelete(item)">
                          <v-icon>mdi-delete-outline</v-icon>
                        </v-btn>
                      </template>
                      <span>カードを削除</span>
                    </v-tooltip>
                  </v-card-actions>
                </template>
                <template v-else>
                  <v-textarea
                    style="max-width: 95%; font-size: 14px; padding-left: 16px"
                    auto-grow
                    autofocus
                    v-model="item.context"
                    :rows="0"
                    no-resize
                    @keydown.enter.exact="setContext(item)"
                  >
                  </v-textarea>
                  <v-btn icon @click="setContext(item)">
                    <v-icon>mdi-check</v-icon>
                  </v-btn>
                  <v-btn icon @click="rollbackContext(item)">
                    <v-icon>mdi-close</v-icon>
                  </v-btn>
                </template>
              </v-card>
            </v-col>
          </v-row>
        </draggable>
      </v-container>
    </v-main>
  </div>
</template>

<script>
import draggable from "vuedraggable";
export default {
  components: {
    draggable,
  },
  head() {
    return {
      title: "デッキ編集",
    };
  },
  data() {
    return {
      deckName: "新規デッキ",
      isEditDeck: false,
      isSaving: false,
      bkDeckName: "",
      isEditDeckName: false,
      cardList: [],
      bkCardList: [],
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
  destroyed() {},
  methods: {
    cardListAdd() {
      let a = {
        order: this.cardList.length,
        context: "追加された",
        bkContext: "",
        edit: false,
      };
      this.cardList.push(a);
      this.isEditDeck = true;
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
    isCardEdit(item) {
      item.edit = true;
      item.bkContext = item.context;
    },
    isCardDuplication(item) {
      let v = {
        order: item.order + 1,
        context: item.context,
        bkContext: "",
        edit: false,
      };
      this.cardList.splice(item.order, 0, v);
      this.cardList.forEach((v, index) => {
        v.order = index;
      });
      this.isEditDeck = true;
    },
    isDeckNameEdit() {
      if (!this.isEditDeckName) {
        this.bkDeckName = this.deckName;
      } else {
        if (this.deckName === "") {
          this.deckName = this.bkDeckName;
        }
        if (!(this.deckName === this.bkDeckName)) {
          this.isEditDeck = true;
        }
      }
      this.isEditDeckName = !this.isEditDeckName;
    },
    listUpdate(evt) {
      this.cardList.forEach((v, index) => {
        v.order = index;
      });
      this.isEditDeck = true;
    },
    setContext(item) {
      let str = item.context;
      str = str.replace(/\r?\n/g, "");
      if (str === "") {
        item.context = item.bkContext;
      }
      if (!(item.context === item.bkContext)) {
        this.isEditDeck = true;
      }
      item.edit = false;
    },
    rollbackContext(item) {
      item.context = item.bkContext;
      item.edit = false;
    },
    async deckSave() {
      this.isSaving = true;
      setTimeout(() => {
        this.isEditDeck = false;
        this.isSaving = false;
      }, 1000);
    },
    toDuel() {
      if (!this.cardList.length > 0) {
        alert("カードは一枚以上ないとだめです！");
        return;
      }
      if (this.isEditDeck) {
        if (
          !confirm(
            "デッキが保存されていません。このまま保存前のデッキで挑んでよろしいでしょうか？"
          )
        ) {
          return;
        }
      }
      alert("デュエルスタート！");
      this.$router.push(`/topic_deck/duel/${this.$route.params.code}`);
    },
  },
};
</script>

<style></style>
