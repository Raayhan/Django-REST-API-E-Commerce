<template>
    <tr>
        <td><router-link :to="item.product.get_absolute_url">{{ item.product.name }}</router-link></td>
        <td>${{ item.product.price }}</td>
        <td>{{ item.quantity }}
            <a class="p-2 is-size-6" @click="decrementQuantity(item)"><i class="fa fa-minus-circle"></i></a>
            <a class="p-1 is-size-6" @click="incrementQuantity(item)"><i class="fa fa-plus-circle"></i></a>
        </td>
        <td>$ {{ getItemTotal(item).toFixed(2) }}</td>
        <td><button class="delete" @click="removeFromCart(item)"></button></td>
    </tr>
</template>
<script>
export default {
    name: 'CartItem',
    props: {
        initialItem:Object
    },
    data() {
        return {
            item:this.initialItem
        }
    },
    methods: {
        getItemTotal(item) {
            return item.quantity * item.product.price
            
        },
        decrementQuantity(item) {
            item.quantity -= 1
            if (item.quantity === 0) {
                this.$emit('removeFromCart',item)
            }
            this.updateCart()
        },
        incrementQuantity(item) {
            item.quantity += 1
            this.updateCart()
        },
        updateCart() {

            localStorage.setItem('cart', JSON.stringify(this.$store.state.cart))
        },
        removeFromCart(item) {
            this.$emit('removeFromCart', item)
            this.updateCart()
        }

    },
   
}
</script>