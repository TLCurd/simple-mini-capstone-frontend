<script>
import { tsImportEqualsDeclaration } from '@babel/types';
import axios from 'axios';
export default {
  data: function () {
    return {
      message: "Welcome to Vue.js!",
      products: [],
      newProductParams: {},
      currentProduct: {},
      editProductParams: {}
    };
  },
  created: function () {
    console.log("In products...");
    // axios.get('http://localhost:3000/products.json').then(response => {
    //   console.log(response.data);
    //   this.products = response.data;
    // });
  },
  methods: {
    productsIndex: function () {
      console.log("In products...");
      axios.get('http://localhost:3000/products.json').then(response => {
        console.log(response.data);
        this.products = response.data;
      });
    },
    createProduct: function () {
      console.log("Creating...");
      var newProductParams = {
        name: this.newProductParams.name,
        description: this.newProductParams.description,
        price: this.newProductParams.price,
        image_url: this.newProductParams.image_url
      }
      axios.post('http://localhost:3000/products.json', newProductParams).then(response => {
        console.log(response.data);
        this.products.push(response.data);
        this.newProductParams = {}
      })
    },
    showProduct: function (theProduct) {
      console.log(theProduct);
      console.log('showing product')
      this.currentProduct = theProduct;
      this.editProductParams = theProduct;
      document.querySelector("#product-details").showModal();
    },
    updateProduct: function () {
      console.log('editing product...')
      // mimic requests.http
      console.log(this.editProductParams);
      axios.patch(`http://localhost:3000/products/${this.editProductParams.id}.json`, this.editProductParams).then(response => {
        console.log(response.data);
      })
    },
    destroyProduct: function (product) {
      console.log(product.id),
        console.log('destroying product...'),
        // make id dynamic
        // remove from html page
        axios.delete(`http://localhost:3000/products/${product.id}`).then(response => {
          var index = this.products.indexOf(product)
          this.products.splice(index, 1);
          console.log(response.data);
        })
    }
  }
};
</script>

<template>
  <div class="home">
    <h1>{{ message }}</h1>
    <br />
    <button v-on:click="productsIndex()">Show all Products</button>
    <br />
    <div v-for="product in products" v-bind:key="product.id">
      <br />
      {{ product.id }}. {{ product.name }} ${{ product.price }}
      <button
        v-on:click="showProduct(product)"
      >More Info</button>
      <br />
      <br />
      <img v-bind:src="product.image_url" />
    </div>
    <br />
    <p>
      Name:
      <input v-model="newProductParams.name" />
    </p>
    <p>
      description:
      <input v-model="newProductParams.description" />
    </p>
    <p>
      price:
      <input v-model="newProductParams.price" />
    </p>
    <p>
      Image Url:
      <input v-model="newProductParams.image_url" />
    </p>
    <button v-on:click="createProduct()">Add a new product</button>

    <dialog id="product-details">
      <form method="dialog">
        <h1>Product info</h1>
        <p>Name: {{ currentProduct.name }}</p>
        <p>Description: {{ currentProduct.description }}</p>
        <p>Price: {{ currentProduct.price }}</p>
        <p>Image: {{ currentProduct.image_url }}</p>

        <p>
          Name:
          <input v-model="editProductParams.name" />
        </p>
        <p>
          Description:
          <input v-model="editProductParams.description" />
        </p>
        <p>
          Price:
          <input v-model="editProductParams.price" />
        </p>
        <p>
          Image:
          <input v-model="editProductParams.image_url" />
        </p>

        <button v-on:click="updateProduct()">Update Product</button>
        <button v-on:click="destroyProduct(currentProduct)">Delete Product</button>

        <button>Close</button>
      </form>
    </dialog>
  </div>
</template>

<style>
img {
  width: 250px;
}
</style>