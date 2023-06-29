<script setup>
	import { ref, inject } from 'vue'
	import axios from 'axios'
	import CheckoutItem from '@/components/CheckoutItem.vue'

	const cartProducts = ref([])
	const totalPrice = ref(0)

	const getCartProducts = () => {
		const storedCartItems = JSON.parse(localStorage.getItem('cartItems')) || []
		cartProducts.value = storedCartItems

		let sum = 0

		storedCartItems.forEach((item, index) => {
			const price = item.price
			const quantity = item.quantity
			const itemTotalPrice = price * quantity
			sum += itemTotalPrice
		})

		totalPrice.value = sum
	}
	getCartProducts()

	const firstname = ref('')
	const lastname = ref('')
	const address = ref('')
	const company = ref('')           // business address
	const building = ref('')          // apartment, suite, etc
	const city = ref('')
	const phone = ref('')
	
	const email = ref('')
	const username = ref('')
	const userId = ref('')

	const makeOrder = async () => {
		if (localStorage.getItem('userId') && localStorage.getItem('email') && localStorage.getItem('username')){

			userId.value = localStorage.getItem('userId')
			email.value = localStorage.getItem('email')
			username.value = localStorage.getItem('username')

			try{
				const response = await axios.post('https://puphomessalesbe.onrender.com/order/make-order/', {
					first_name: firstname.value,
					last_name: lastname.value,
					address: address.value,
					company: company.value,
					building: building.value,
					city: city.value,
					phone: phone.value,
					userId: userId.value,
					email: email.value,
					username: username.value,
					total_ammount: totalPrice.value,
					cartProducts: cartProducts.value
				})

				console.log(response.data)
				setTimeout(()=>{
					document.querySelector('.ch-success').style.display = 'block'
				}, 4000)

				localStorage.setItem('cartItems', JSON.stringify([]))
				cartProducts.value = []
			}catch(e){
				console.error(e.message);
				document.querySelector('.ch-warn').style.display = 'block'
			}

		}else{
			window.location.href = '/login'
		}
	}
</script>

<template>
	<div class="checkout">
		
		<section class="checkout-v">
			<h2>Contact Information</h2>
			<hr>
			<div class="ch-information">
				
				<h3>Shipping Address</h3>
				<div>
					<input type="text" v-model="firstname" placeholder="First Name">
					<input type="text" v-model="lastname" placeholder="Last Name">
				</div>
				<input type="text" v-model="address" placeholder="Address">
				<input type="text" v-model="company" placeholder="Company (business address) (optional)">
				<input type="text" v-model="building" placeholder="Apartment, suite, etc (optional)">
				<input type="text" v-model="city" placeholder="City">
				<input type="text" v-model="phone" placeholder="Phone (optional)">

			</div>
			<p class="ch-warn">Finish this form, please.</p>
		</section>
		<section class="checkout-v">
			
			<h1>Checkout</h1>
			<ul class="ch-cart">
				<CheckoutItem v-for="product in cartProducts" :key="product.id" 
					:img="product.thumbnail" 
					:title="product.name" 
					:price="product.price" 
					:quantity="product.quantity"
				/>
			</ul>

			<div class="ch-total">
				<p>Total</p>
				<h3><span>USD</span>${{ totalPrice }}</h3>
			</div>

			<button class="btn2" @click="makeOrder">Continue</button>

			<p class="ch-success">
				Order placed successfully!
				<RouterLink to="/user-page">See my orders</RouterLink>
			</p>

		</section>

	</div>
</template>

<style scoped>
	.checkout{
		width: 100%;
		display: flex;
		padding: 1rem;
		gap: .5rem;
		justify-content: space-between;
	}
	.checkout-v{
		width: 45%;
		display: flex;flex-direction: column;
		gap: .5rem;
	}
	.ch-cart{
		width: 100%;
		display: flex;flex-direction: column;
		gap: .5rem;
		max-height: 90vh;
		overflow-y: scroll;
		padding: 1rem;
	}

	.ch-total{
		padding: 2rem;
		display: flex;align-items: center;justify-content: space-between;
	}
	.ch-total h3{
		font-size: 3rem;
		font-family: 'lemon', sans-serif;
	}
	.ch-total p{font-size: 1.5rem;}
	.ch-total h3 span{
		font-size: 1.5rem;
	}
	.ch-information{
		padding: 2.5rem 0;
		display: flex;flex-direction: column;
		width: 100%;
		gap: .5rem;
	}
	.ch-information div{
		display: flex;align-items: center;width: 100%;
		gap: .5rem;
	}
	.ch-information input{
		width: 100%;
	}
	.ch-warn {
		display: none;
		width: 100%;
		padding: 1rem;
		background: #500;color: #fff;
		text-align: center;
	}
	.checkout-v .btn2{
		border: 1px solid #ddd;
	}

	.ch-success{
		display: none;
		width: 100%;
		padding: 1rem;
		background: #082;color: #fff;
		text-align: center;
	}
	.ch-success a{
		color: #fff;
		font-size: 1.3rem;
	}
	@media (max-width: 720px) {
		.checkout{
			flex-direction: column;
		}
		.checkout-v{
			width: 100%;
		}
		.ch-cart{
			padding: 1rem 0;
		}
	}
</style>