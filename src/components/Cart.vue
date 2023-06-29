<script setup>
    import { ref, onMounted, inject } from 'vue'
    import axios from 'axios'
    import CartItem from '@/components/CartItem.vue'
	
	const cartProducts = inject('cartProducts')
	const getCartProducts = inject('getCartProducts')

	const removeFromCart = (product) => {
		const cartItems = JSON.parse(localStorage.getItem('cartItems')) || []
		const index = cartItems.findIndex((item) => item.id === product.id)
	
		// remove from localStorage if quantity is 0
		if (index !== -1) {
			if (cartItems[index].quantity > 1){
				let previousQuantity = cartItems[index].quantity
				cartItems[index].quantity--
				cartItems[index].price = (cartItems[index].price / previousQuantity) * cartItems[index].quantity
			}else{
				cartItems.splice(index, 1)
			}
		}

		// update cartItems
		localStorage.setItem('cartItems', JSON.stringify(cartItems))
		getCartProducts()
	}

	const goToCheckout = () => {window.location.href='/checkout'}

	getCartProducts()

	const showCart = ref(false)
	const toggleCart = () => {
		showCart.value = !showCart.value
	}
</script>

<template>

	<button class="fa-solid fa-cart-shopping cartBtn" :style="{ 
		right: showCart ? '1.25rem': '1.25rem',
		bottom: showCart ? '1.8rem' : '1.8rem',
		height: showCart ? 'initial' : 'fit-content',
		zIndex: showCart ? '22' : '19',
	}" @click="toggleCart"></button>

	<section class="cart" :class="{ 'visible': showCart }" v-if="cartProducts.length > 0">
		<ul class="cart-items">
			<h2>Cart</h2>

			<CartItem v-for="cartProduct in cartProducts" :key="cartProduct.id"
				:img="cartProduct.thumbnail" 
				:title="cartProduct.name" 
				:price="cartProduct.price" 
				:quantity="cartProduct.quantity" 
				@remove-from-cart="removeFromCart(cartProduct)"
			/>

		</ul>
		<button class="btn1" @click="goToCheckout">Go to CheckOut page</button>
	</section>

	<div class="cart empty-cart" :class="{ 'visible': showCart }" v-else>
		<p>The cart is empty.</p>
		<a href="/#products-grid">> <span>Begin Shopping</span></a>
	</div>
</template>

<style scoped>
	.cart-items h2{
		font-family: 'lemon', sans-serif;
		color: #000;
		width: 100%;
		text-align: center;
	}
	.cart.empty-cart{
		display: flex;
		flex-direction: column;
		align-items: center;
	}
	.cart.empty-cart p{
		color: #000;
		font-size: 2rem;
	}
	.cart.empty-cart a{
		color: #000;
		text-decoration: none;
	}
	.cart.empty-cart a:hover span{
		margin-left: .5rem;
	}
	.cart.visible{
		width: 40vw;
		padding: 1rem 4rem 1rem 1rem;
	}
	.cart{
		width: 0vw;
		padding: 0rem;
		height: 100%;
		position: fixed;
		right: 0;top: 0;
		overflow-x: hidden;overflow-y: scroll;
		z-index: 3;

		background: #fff;
		box-shadow: 0 0 1rem #444;
		color: #fff;
	}
	li{
		list-style-type: none;
	}
	.cart .btn1 {
		width: 100%;
		margin-top: .5rem;
	}
	.cartBtn{
		font-size: 1.5rem;
		position: fixed;
		top: 6rem;
		right: 1.25rem;
		padding: 12px;
		cursor: pointer;
		border-radius: 100px;
		border:none;
		box-shadow:0 0 1rem #888;
	}
	.cartBtn:hover{
		box-shadow:0 0 1.3rem #ccc;
		background: var(--a);
		color: #fff;
	}

	.cart-items{
		max-height: 90vh;
		overflow-y: scroll;
	}

	@media (max-width: 900px) {
		.cart.visible{
			width: 100%;
			padding: 5.6rem 6rem 1rem 1rem;
		}
		.cartBtn{
			z-index: 4;
		}
	}
</style>