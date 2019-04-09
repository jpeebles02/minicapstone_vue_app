<template>
    <div class="products-show">
        <h2>{{product.name}}</h2>
        <img v-bind:src="product.primary_image" v-bind:alt="product.name" />
        <p>Price: {{product.the_total_price_is}}</p>
        <p>Description: {{product.description}}</p>
        <p>Rating: {{product.the_rating_is}}</p>
        <p>Number of Products: {{product.number_of_products}}</p>
        <button v-on:click="destroyProduct(product)">Delete Product</button>

        <router-link v-bind:to="`/products/${product.id}/edit`">Edit this product | </router-link>
        <router-link to="/"> Back to all products </router-link>
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
            destroyProduct: function(product) {
                axios.delete("/api/products/" + product.id).then(response => {
                    console.log("Successfully deleted product", response.data);
                    this.$router.push("/");
                });
            }
        }
    };
</script>