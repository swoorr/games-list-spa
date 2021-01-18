<template>
  <div class="gamelist container">
    <div class="page-title">
      <h2>{{ pageGame }} games results...</h2>
      <div class="row">
        <div class="col-md-12 col-center-block">
          <form class="col-6" @submit.prevent="submitForm()">
            <div class="form-group">
              <label for=""></label>
              <input
                type="text"
                v-model="pageGameInput"
                class="form-control"
                placeholder="game keyword, please type"
              />
            </div>
            <button type="submit" class="form-control btn border btn-warning">
              Submit
            </button>
          </form>
        </div>
      </div>
    </div>
    <div class="row">
      <template v-if="gameList.length == 0">
        <div class="card w-100">
          <div class="card-body col-12 pt-5 shadow-lg">
            <h2>Not found {{ pageGame }}</h2>
            <div class="alert alert-info">
              <span>hahahaha... not found</span>
            </div>
          </div>
        </div>
      </template>
      <div
        class="col-lg-4 col-md-6"
        v-for="x in gameList"
        v-if="gameList.length"
      >
        <div class="item">
          <div
            class="head"
            v-bind:style="{
              backgroundImage: 'url(' + x.thumb + ')',
              color: 'red',
            }"
          >
          </div>
          <div class="body">
            <div class="row">
              <div class="col-9">
                <h2>{{ x.external }}</h2>
              </div>
              <div class="col-3">
                <div class="icons">
                  <div class="inc">
                    <div class="hot"></div>
                    <span class="savings">{{ hasPrice(x) }}</span>
                  </div>
                </div>
              </div>
            </div>
          </div>
          <div class="footer">
            <div class="row">
              <div class="col-8">
                <span class="price">{{ getPrice(x) }}</span>
                <span class="old-price">{{ oldPrice(x) }}</span>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { defineProps, reactive } from "vue";

defineProps({
  msg: String,
});

const state = reactive({ pageGameInput: "", pageGame: "batman" });
</script>

<script>
import axios from "axios";
export default {
  name: "home",
  data: function () {
    return {
      gameList: [],
      gamePrices: [],
      pageGameInput: "",
      pageGame: "batman",
    };
  },
  computed: {},

  methods: {
    getPrice: function (x) {
      let prc = this.gamePrices[x.gameID];
      if (prc != undefined) {
        return "$" + prc.deals[0].price;
      }
      return "";
    },
    oldPrice: function (x) {
      let prc = this.gamePrices[x.gameID];
      if (this.gamePrices[x.gameID] != undefined) {
        if (prc.deals[0].retailPrice != prc.deals[0].price) {
          return "$" + prc.deals[0].retailPrice;
        }
      }
      return "";
    },
    hasPrice: function (x) {
      let prc = this.gamePrices[x.gameID];
      if (this.gamePrices[x.gameID] != undefined) {
        if (prc.deals[0].retailPrice != prc.deals[0].price) {
          return Math.floor(prc.deals[0].savings) + "%";
        }
      }
      return false;
    },
    getPrices: async function (gameIDS) {
      this.gamePrices = await axios.get(
        "https://www.cheapshark.com/api/1.0/games?ids=" + gameIDS,
      );
      this.gamePrices = this.gamePrices.data;

      this.gameList = this.gameList.filter((d) => {
        return this.hasPrice(d);
      });
    },
    getGames: async function (x) {
      var data = await axios.get(
        "https://www.cheapshark.com/api/1.0/games?title=" +
          x +
          "&limit=60&exact=0",
      );
      this.gameList = data.data;

      var gameIDS = "";
      this.gameList.map((d) => {
        gameIDS += d.gameID + ",";
      });

      gameIDS = gameIDS.substr(0, gameIDS.length - 1);
      this.getPrices(gameIDS);
    },
    submitForm: function () {
      if (this.pageGame != this.pageGameInput) {
        this.getGames(this.pageGameInput);
      }
      if (this.gameList.length > 1) {
        this.pageGame = this.pageGameInput;
      }
    },
  },

  created: async function () {
    this.getGames(this.pageGame);
  },
};
</script>

<style lang="scss"></style>
