<template>
  <div class="home">
    <h1>{{ message }}</h1>
    <div>
      <button v-on:click="setSortAttribute('name')">Sort by name</button>
      <button v-on:click="setSortAttribute('price')">Sort by price(high to low)</button>
    </div>
    Search by Name of Price 
    <input type="text" v-model="searchFilter" list="names"/>
    <datalist id="names">
      <option v-for="product in products">{{ product.name }}</option>
    </datalist>
    <div v-for="product in orderBy(filterBy(products, searchFilter, 'name', 'price'), sortAttribute)">
      <h2> {{product.name}}</h2>
      <img v-bind:src="product.primary_image" v-bind:alt="product.name" />
      <router-link v-bind:to="`/products/${product.id}`">More info</router-link>
    </div>
  </div>
</template>

<style>
img {
  width: 350px;
}
</style>

<script>
var axios = require("axios");
import Vue2Filters from "vue2-filters";

export default {
  mixins: [Vue2Filters.mixin],
  data: function() {
    return {
      message: "Welcome to Sportr",
      products: [],
      searchFilter: "",
      sortAttribute: "name"
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
      this.sortAttribute = inputAttribute;
    }
  }
};
</script>