<template>
    <div class="page-sign-up">
        <div class="columns">
            <div class="column is-4 is-offset-4">
                <h1 class="title">Sign up</h1>
                <form @submit.prevent="submitForm">
                    <div class="field">
                        <label for="username">Username</label>
                        <div class="control">
                            <input type="text" class="input" v-model="username" placeholder="Enter Username">
                        </div>
                    </div>
                    <div class="field">
                        <label for="password">Password</label>
                        <div class="control">
                            <input type="password" class="input" v-model="password" placeholder="Enter Password">
                        </div>
                    </div>
                    <div class="field">
                        <label for="password2">Confirm Password</label>
                        <div class="control">
                            <input type="password" class="input" v-model="password2" placeholder="Confirm Password">
                        </div>
                    </div>
                    <div class="notification is-danger has-text-white" v-if="errors.length">
                        <ul v-for="error in errors" v-bind:key="error">
                       
                            <li>{{ error }}</li>
                       

                        </ul>

                    </div>
                    <div class="field">
                        <div class="control">
                            <button class="button is-dark">Sign up</button>
                        </div>
                    </div>
                    <hr>
                    Or <router-link to="/log-in">Click here</router-link> to log in!
                </form>
            </div>
        </div>

    </div>
</template>
<script>
import axios from 'axios'
import { toast } from "bulma-toast"

export default {
    name: 'SignUp',
    data() {
        return {
            username: '',
            password: '',
            password2: '',
            errors:[]
        }
    },
    methods: {
        submitForm() {
            this.errors = []
            if (this.username === '') {
                this.errors.push('The username is missing')
            }
            if (this.password === '') {
                this.errors.push('The password is missing')
            }
            if (this.password2 === '') {
                this.errors.push('The passwords doesn\'t match')
            }
            if (!this.errors.length) {
                const formData = {
                    username: this.username,
                    password:this.password
                }
                axios
                    .post("/api/v1/users/", formData)
                    .then(response => {
                            toast({
                                message: 'Account has been created. Please log in!',
                                type: 'is-success has-text-white',
                                dismissible: true,
                                pauseOnHover: true,
                                duration: 2000,
                                position: 'bottom-right',
                            })
                            this.$router.push('/log-in')
                    })
                    .catch(error => {
                        if (error.response) {
                            for (const property in error.response.data) {
                                this.errors.push(`${property}: ${error.response.data[property]}`)
                            }

                            console.log(JSON.stringify(error.response.data))
                        }
                        else if (error.message) {
                            this.errors.push('Something went wrong. Please try again later.')
                            console.log(JSON.stringify(error))
                        }
                    })
            }
            
        }
    }
    
}
</script>