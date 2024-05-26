<template>
    <div class="page-search">
        <div class="columns is-multiline">
            <div class="column is-12">
                <h1 class="title">Search</h1>
                <h2 class="is-size-5 has-text-gray">Search term : "{{ query }}"</h2>

            </div>
            <ProductBox v-for="product in products" v-bind:key="product.id" v-bind:product="product">

            </ProductBox>
        </div>

    </div>

</template>
<script>
import axios from 'axios'
import ProductBox from '@/components/ProductBox.vue'
import { toast } from 'bulma-toast'

export default {

    name: 'Search',
    components: {
        ProductBox
    },
    data() {
        return {
            products: [],
            query:''
            
        }
    },
    mounted() {
        document.title = 'Search | Djackets'
        let uri = window.location.search.substring(1)
        let params = new URLSearchParams(uri)

        if (params.get('query')) {
            this.query = params.get('query')
            this.performSearch()
        }

    },
    methods: {
        async performSearch() {

            this.$store.commit('setIsLoading', true)
            await axios
                .post('/api/v1/products/search/',{'query':this.query})
                .then(response => {
                    this.products = response.data
                    if (this.products.length < 1) {
                        toast({
                            message: 'No products available.Please try again.',
                            type: 'is-danger has-text-white',
                            dismissible: true,
                            pauseOnHover: true,
                            duration: 2000,
                            position: 'bottom-right',

                        })
                    }
                    
                })
                .catch(error => {
                    console.log(error)
                })
            this.$store.commit('setIsLoading', false)
            
        }
    }
}
</script>