<template>
  <div>
    <v-app-bar style="position: fixed" app>
      <v-container>
        <v-row>
          <v-col cols="auto">
            <h2 class="mt-1" style="margin-top: auto" v-if="!isEditDeckTitle">
              {{ deckName }}
            </h2>
            <v-text-field v-else></v-text-field>
          </v-col>
          <v-col cols="auto">
            <v-divider vertical></v-divider>
          </v-col>
          <v-col cols="auto">
            <v-btn depressed style="background-color: inherit" icon>
              <v-icon>mdi-square-edit-outline</v-icon>
            </v-btn>
          </v-col>
          <v-col cols="auto">
            <v-btn depressed style="background-color: inherit" icon>
              <v-icon>mdi-content-copy</v-icon>
            </v-btn>
          </v-col>
          <v-col> </v-col>
          <v-col cols="auto" style="margin-left: auto">
            <v-btn depressed style="background-color: inherit" icon>
              <v-icon>mdi-plus-circle</v-icon>
            </v-btn>
          </v-col>
          <v-col cols="auto" style="margin-left: auto">
            <v-btn
              class="mt-1"
              style="
                border-radius: 20px;
                font-weight: bold;
                font-size: 16px;
                color: white;
              "
              depressed
              color="#004BB1"
              @click="DuelStart()"
            >
              保存してデュエルへ
            </v-btn>
          </v-col>
          <v-col cols="auto" style="margin-left: auto">
            <v-btn depressed style="background-color: inherit" icon>
              <v-icon>mdi-cog-outline</v-icon>
            </v-btn>
          </v-col>
        </v-row>
      </v-container>
    </v-app-bar>
    <v-main class="mt-5">
      <v-container>
        <draggable>
          <v-row v-for="item in deckList">
            <v-col>
              <v-card style="display: flex">
                <v-icon class="ml-2"> mdi-drag-vertical-variant</v-icon>
                <v-card-text style="max-width: 100%; white-space: pre-line"
                  >{{ item.context }}
                  これはサンプルっす。
                </v-card-text>
                <v-card-action style="margin-left: auto">
                  <v-btn icon>
                    <v-icon>mdi-content-copy</v-icon>
                  </v-btn>
                </v-card-action>
                <v-card-action>
                  <v-btn depressed style="background-color: inherit" icon>
                    <v-icon>mdi-square-edit-outline</v-icon>
                  </v-btn>
                </v-card-action>
                <v-card-action>
                  <v-btn depressed style="background-color: inherit" icon>
                    <v-icon>mdi-delete-outline</v-icon>
                  </v-btn>
                </v-card-action>
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
      isEditDeckTitle: false,
      deckList: [],
    };
  },
  created() {
    for (let index = 0; index < 10; index++) {
      let a = {
        order: index,
        context: `サンプル${index}`,
      };
      this.deckList.push(a);
    }
  },
  methods: {},
};
</script>

<style></style>
