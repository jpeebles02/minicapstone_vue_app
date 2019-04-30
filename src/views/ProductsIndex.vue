<template>
  <div class="home">
    <div>
      <multiselect v-model="value2" v-bind:options="options" v-bind:multiple="true"></multiselect>
    </div>
    <h1>{{ message }}</h1>
    <label>{{ selectedEvent }}</label>
    <vue-instant
      :suggestOnAllWords="true"
      :suggestion-attribute="suggestionAttribute"
      v-model="value"
      :disabled="false"
      @input="changed"
      @click-input="clickInput"
      @click-button="clickButton"
      @selected="selected"
      @enter="enter"
      @key-up="keyUp"
      @key-down="keyDown"
      @key-right="keyRight"
      @clear="clear"
      @escape="escape"
      :show-autocomplete="true"
      :autofocus="false"
      :suggestions="suggestions"
      name="customName"
      placeholder="custom placeholder"
      type="google"
    ></vue-instant>
    <div>
      <button v-on:click="setSortAttribute('name')">Sort by name</button>
      <button v-on:click="setSortAttribute('price')">Sort by price</button>
    </div>
    Search by Name of Price
    <input type="text" v-model="searchFilter" list="names" />
    <datalist id="names">
      <option v-for="product in products">{{ product.name }}</option>
    </datalist>
    <transition-group appear enter-active-class="animated rollIn" leave-active-class="animated heartBeat">
      <div
        v-for="product in orderBy(filterBy(products, searchFilter, 'name', 'price'), sortAttribute, sortAscending)"
        v-bind:key="product.id"
      >
        <h2>{{ product.name }}</h2>
        <img v-bind:src="product.primary_image" v-bind:alt="product.name" />
        <router-link v-bind:to="`/products/${product.id}`">More info</router-link>
      </div>
    </transition-group>
  </div>
</template>

<style src="vue-multiselect/dist/vue-multiselect.min.css">
img {
  width: 350px;
}
</style>

<script>
var axios = require("axios");
import Vue2Filters from "vue2-filters";
import Multiselect from "vue-multiselect";
import Vue from "vue";
import "vue-instant/dist/vue-instant.css";
import VueInstant from "vue-instant";
Vue.use(VueInstant);

export default {
  mixins: [Vue2Filters.mixin],
  components: {
    Multiselect
  },
  data: function() {
    return {
      message: "Welcome to Sportr",
      products: [],
      searchFilter: "",
      sortAttribute: "name",
      sortAscending: 1,
      value2: [],
      options: ["list", "of", "options"],
      value: "",
      suggestionAttribute: "original_title",
      suggestions: [],
      selectedEvent: ""
    };
  },
  created: function() {
    // RUBY WEB REQUEST
    // response = HTTP.get("/api/products")
    // @products = response.data;
    // JAVASCRIPT WEB REQUEST
    axios.get("/api/products").then(response => {
      this.products = response.data;
      console.log(this.products[1].images[0].url);
      console.table(this.products);
    });
  },

  methods: {
    setSortAttribute: function(inputAttribute) {
      if (this.sortAttribute === inputAttribute) {
        if (this.sortAscending === 1) {
          this.sortAscending = -1;
        } else {
          this.sortAscending = 1;
        }
      } else {
        this.sortAscending = 1;
      }
      this.sortAttribute = inputAttribute;
    },
    clickInput() {
      this.selectedEvent = "click input";
    },
    clickButton() {
      this.selectedEvent = "click button";
    },
    selected() {
      this.selectedEvent = "selection changed";
    },
    enter() {
      this.selectedEvent = "enter";
    },
    keyUp: function() {
      this.selectedEvent = "keyup pressed";
    },
    keyDown: function() {
      this.selectedEvent = "keyDown pressed";
    },
    keyRight: function() {
      this.selectedEvent = "keyRight pressed";
    },
    clear: function() {
      this.selectedEvent = "clear input";
    },
    escape: function() {
      this.selectedEvent = "escape";
    },
    changed: function() {
      var that = this;
      this.suggestions = [];
      axios
        .get("https://api.themoviedb.org/3/search/movie?api_key=342d3061b70d2747a1e159ae9a7e9a36&query=" + this.value)
        .then(function(response) {
          response.data.results.forEach(function(a) {
            that.suggestions.push(a);
          });
        });
    }
  }
};
</script>
