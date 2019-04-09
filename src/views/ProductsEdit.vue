<template>
  <div class="products-edit">
    <div>
      <h4>Edit product</h4>
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
        <button v-on:click="updateProduct(product)">Update product</button>
        <router-link to="/">Cancel</router-link>
      </div>
  </div>
</template>

<script>
import axios from "axios";

    export default {
        data: function() {
            return {
                product: {}
            };
        },
        created: function() {
            axios.get("/api/products/" + this.$route.params.id).then(response => {
                this.product = response.data;
            });
        },
        methods: {
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
                    this.$router.push("/");
                });
            }
        }
    };
</script>