<template>
  <div class="products-new">
    <h1>New Product</h1>
    <ul>
      <li v-for="error in errors">{{ error }}</li>
    </ul>
    <form v-on:submit.prevent="createProduct()">
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
      <input type="submit" value="Create product" />
    </form>
  </div>
</template>

<script>
var axios = require("axios");

export default {
  data: function() {
    return {
      newProductName: "",
      newProductPrice: "",
      newProductDescription: "",
      newProductRating: "",
      newNumberOfProducts: "",
      newProductImageUrl: "",
      errors: []
    };
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
          this.$router.push("/");
        })
        .catch(error => {
          console.log(error.response.data.errors);
          this.errors = error.response.data.errors;
        });
    }
  }
};
</script>
