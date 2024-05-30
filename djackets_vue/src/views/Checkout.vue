<template>
    <div class="page-checkout">
        <div class="columns is-multiline">
            <div class="column is-12">
                <h1 class="title">
                    Checkout
                </h1>
            </div>
            <div class="column is-12 box p-6">
                <table class="table is-fullwidth">
                    <thead>
                        <tr>
                            <th>Product</th>
                            <th>Price</th>
                            <th>Quantity</th>
                            <th>Total</th>
                        </tr>
                    </thead>
                    <tbody>
                        <tr v-for="item in cart.items" v-bind:key="item.product.id">
                            <td>{{ item.product.name }}</td>
                            <td>${{ item.product.price }}</td>
                            <td>{{ item.quantity }}</td>
                            <td>${{ getItemTotal(item).toFixed(2) }}</td>
                        </tr>
                    </tbody>
                    <tfoot>
                        <tr>
                            <td colspan="2">Total</td>
                            <td>{{ cartTotalLength }}</td>
                            <td>${{ cartTotalPrice.toFixed(2) }}</td>
                        </tr>
                    </tfoot>
                </table>
            </div>
            <div class="column is-12 box p-6">
                <h2 class="subtitle is-flex is-justify-content-center">
                    Shipping Details
                </h2>
                <p class="has-text-gray mb-4 is-flex is-justify-content-center">* All fields are required</p>
            </div>
        </div>
        <div class="columns is-multiline p-6">
            <div class="column is-3"></div>
            <div class="column is-3">
                <div class="field">
                    <label>First name*</label>
                    <div class="control">
                        <input type="text" class="input" v-model="first_name" placeholder="Enter First Name">
                    </div>
                </div>

                <div class="field">
                    <label>Last name*</label>
                    <div class="control">
                        <input type="text" class="input" v-model="last_name" placeholder="Enter Last Name">
                    </div>
                </div>

                <div class="field">
                    <label>E-mail*</label>
                    <div class="control">
                        <input type="email" class="input" v-model="email" placeholder="Enter Email Address">
                    </div>
                </div>

                <div class="field">
                    <label>Phone*</label>
                    <div class="control">
                        <input type="text" class="input" v-model="phone" placeholder="Enter Phone Number">
                    </div>
                </div>
            </div>
            <div class="column is-3">
                <div class="field">
                    <label>Address*</label>
                    <div class="control">
                        <input type="text" class="input" v-model="address" placeholder="Enter Full Address">
                    </div>
                </div>

                <div class="field">
                    <label>Zip code*</label>
                    <div class="control">
                        <input type="text" class="input" v-model="zipcode" placeholder="Enter Area Zip Code">
                    </div>
                </div>

                <div class="field">
                    <label>Place*</label>
                    <div class="control">
                        <input type="text" class="input" v-model="place" placeholder="Enter Place">
                    </div>
                </div>
            </div>
            <div class="column is-3"></div>
            <div class="mx-auto">
                <div class="notification is-danger mt-4 has-text-white" v-if="errors.length">
                    <p v-for="error in errors" v-bind:key="error">{{ error }}</p>
                </div>
            </div>


            <hr>


        </div>
        <div id="card-element" class="mb-5">

            <template v-if="cartTotalLength">
                <hr>
                <div class="is-flex is-justify-content-center">
                    <button class="button is-link" @click="submitForm">Pay with
                        Stripe</button>
                </div>

            </template>
        </div>
    </div>

</template>
<script>
import axios from 'axios'
import { toast } from 'bulma-toast'

export default {
    name: 'Checkout',
    data() {
        return {
            cart: {
                items: []
            },
            stripe: {},
            card: {},
            first_name: '',
            last_name: '',
            email: '',
            phone: '',
            address: '',
            zipcode: '',
            place: '',
            errors: []

        }
    },
    mounted() {
        document.title = 'Checkout | Djackets'
        this.cart = this.$store.state.cart
    },
    methods: {
        getItemTotal(item) {
            return item.quantity * item.product.price
        },
        submitForm() {
            this.errors = []

            if (this.first_name === '') {
                this.errors.push('The first name field is missing!')
            }

            if (this.last_name === '') {
                this.errors.push('The last name field is missing!')
            }

            if (this.email === '') {
                this.errors.push('The email field is missing!')
            }

            if (this.phone === '') {
                this.errors.push('The phone field is missing!')
            }

            if (this.address === '') {
                this.errors.push('The address field is missing!')
            }

            if (this.zipcode === '') {
                this.errors.push('The zip code field is missing!')
            }

            if (this.place === '') {
                this.errors.push('The place field is missing!')
            }

            if (!this.errors.length) {
                this.$store.commit('setIsLoading', true)

                this.stripe.createToken(this.card).then(result => {
                    if (result.error) {
                        this.$store.commit('setIsLoading', false)

                        this.errors.push('Something went wrong with Stripe. Please try again')

                        console.log(result.error.message)
                    } else {
                        this.stripeTokenHandler(result.token)
                    }
                })
            }
        }

    },
    computed: {
        cartTotalLength() {
            return this.cart.items.reduce((acc, curVal) => {
                return acc += curVal.quantity
            }, 0)
        },
        cartTotalPrice() {
            return this.cart.items.reduce((acc, curVal) => {
                return acc += curVal.product.price * curVal.quantity
            }, 0)
        }
    }
}
</script>