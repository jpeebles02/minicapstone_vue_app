<template>
  <div class="home">
    <h1>{{ message }}</h1>
    <h1>New Product</h1>
    <ul>
      <li v-for="error in errors">{{ error }}</li>
    </ul>
    <div>
      Name: 
      <input type="text" v-model="newProductName" />
      Price: 
      <input type="text" v-model="newProductPrice" />
      Description: 
      <input type="text" v-model="newProductDescription" />
      Rating:
      <input type="text" v-model="newProductRating" />
      Number of Products:
      <input type="text" v-model="newNumberOfProducts" />
    </div>
    <button v-on:click="createProduct()">Create Product</button>
    <div v-for="product in products">
      <h2>Name: {{product.name}}</h2>
      <img v-bind:src="product.primary_image" v-bind:alt="product.name" />
      <div>
        <button v-on:click="showProduct(product)">More info</button>
      </div>
      <div v-if="product === currentProduct">
        <p>Price: {{product.the_total_price_is}}</p>
        <p>Description: {{product.description}}</p>
        <p>Rating: {{product.the_rating_is}}</p>
        <p>Number of Products: {{product.number_of_products}}</p>
        <div>
            <h4>Edit Product </h4>
            Name: 
            <input type="text" v-model="product.name" />
            Price: 
            <input type="text" v-model="product.price" />
            Description:
            <input type="text" v-model="product.description" />
            Rating:
            <input type="text" v-model="product.rating" />
            Number of Products:
            <input type="text" v-model="product.number_of_products" />
            Image url: 
            <input type="text" v-model="product.image_url" />
            <button v-on:click="updateProduct(product)">Update Product</button>
            <button v-on:click="destroyProduct(product)">Delete Product</button>
        </div>
      </div>
    </div>
  </div>
</template>

<style>
img {
  width: 400px;
}
</style>

<script>
var axios = require("axios");

export default {
  data: function() {
    return {
      message: "Welcome to Sportr",
      products: [],
      newProductName: "",
      newProductPrice: "",
      newProductDescription: "",
      newProductRating: "",
      newNumberOfProducts: "",
      newProductImageUrl: "",
      currentProduct: null,
      errors: []
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
    });
  },
  methods: {
    createProduct: function() {
      console.log("Create the product...");
      this.errors = [];
      var params = {
        name: this.newProductName,
        price: this.newProductPrice,
        description: this.newProductDescription,
        rating: this.newProductRating,
        number_of_products: this.newProductNumberOfProducts
      };
      axios
        .post("/api/products", params)
        .then(response => {
          this.products.push(response.data);
      })
        .catch(error => {
          console.log(error.response.data.errors);
          this.errors = error.response.data.errors;
        });
    },
    showProduct: function(product) {
      if (this.currentProduct === product) {
        this.currentProduct = null;
      } else {
        this.currentProduct = product;
      }
    },
    updateProduct: function(product) {
      var params = {
        name: product.name,
        price: product.price,
        description: product.description,
        rating: product.rating,
        number_of_products: product.number_of_products
      };
      axios.patch("/api/products/" + product.id, params).then(response => {
        console.log("Successfully updates", response.data);
        product = response.data;
      });
    },
    destroyProduct: function(product) {
      axios.delete("/api/products/" + product.id).then(response => {
        console.log("Successfully deleted product", response.data);
        var index = this.products.indexOf(product);
        this.products.splice(index, 1);
      });
    }
  }
};
</script>