<template>
  <div class="home">
    <section class="hero is-small is-dark mb-6">
      <div class="hero-body has-text-centered">
        <p class="title mb-6">
          Welcome to Djackets Online
        </p>
        <p class="subtitle">
          The best jacket store online
        </p>
      </div>
    </section>
    <div class="columns is-multiline">
      <div class="column is-12">
        <h2 class="is-size-2 has-text-centered">
          Latest Products
        </h2>
      </div>
      <ProductBox v-for="product in latestProducts" v-bind:key="product.id" v-bind:product="product"></ProductBox>
    </div>
  </div>
</template>

<script>

import axios from 'axios'
import ProductBox from '@/components/ProductBox.vue'

export default {
  name: 'HomeView',
  data() {
    return {
      latestProducts: []
    }
  },
  components: {
    ProductBox
  },
  mounted() {
    this.getLatestProducts()
    document.title = 'Home' + ' | Djackets'
  },
  methods: {
    async getLatestProducts() {
      this.$store.commit('setIsLoading', true)
      await axios
        .get('api/v1/latest-products')
        .then(response => { this.latestProducts = response.data })
        .catch(error => {
          console.log(error)
        })
      this.$store.commit('setIsLoading', false)
    }
  }
}
</script>
