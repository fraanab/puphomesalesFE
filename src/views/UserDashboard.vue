<script setup>
	import { ref } from 'vue'
	import axios from 'axios'

	const username = localStorage.getItem('username')
	const orders = ref([])
	const cards_data = ref([])
	const total_spent_card = ref(0)
	const userId = ref('')

	const getOrders = async () => {
		userId.value = localStorage.getItem('userId')
		if (userId.value) {

			try{
				const response = await axios(`https://puphomessalesbe.onrender.com/order/get-orders/${userId.value}`)

				orders.value = response.data.orders
				cards_data.value = response.data.cards_data
				total_spent_card.value = response.data.cards_data['totalSpent']
				console.log(cards_data.value);
			}catch(e){
				console.error(e.message)
			}

		}else{
			window.location.href = '/login'
		}
	}

	getOrders()
</script>

<template>
	<section v-if="userId" class="u-dashboard">
		
		<h1>{{ username }}</h1>
		<h2>Dashboard</h2>
		<hr>
		<h2 style="text-align: center;padding: 2rem 0;">Your Orders</h2>
		<hr>
		<ul>
			<li>Order ID</li>
			<li>Product</li>
			<li>Price</li>
			<li>Quantity</li>
			<li>To</li>
			<li>TOTAL</li>
		</ul>
		<ul v-for="i in orders">
			<li>{{ i.orderId }}</li>
			<li>{{ i.productName }}</li>
			<li>${{ i.productPrice }}</li>
			<li>x {{ i.productQuantity }}</li>
			<li>{{ i.orderAddress }}</li>
			<li>${{ i.orderTotal }}</li>
		</ul>

		<div class="dash-cards">
			
			<div class="dash-card">
				<p>Total Spent</p>
				<p>$ {{ total_spent_card }}</p>
			</div>

		</div>
	</section>

	<div v-else>
		<p>404</p>
	</div>
</template>

<style scoped>
	.dash-cards{
		display: flex;flex-direction: row;flex-wrap:wrap;
		align-items: center;justify-content: space-evenly;
		margin-top: 1rem;
	}
	.dash-card{
		padding: 1rem;
		border: 1px solid #ccc;
		width: fit-content;
	}
	.dash-card p:first-of-type{
		font-size: 1.5rem;
		color: #555;
	}
	.dash-card p:last-of-type{
		font-size: 3rem;
		font-weight: bold;
	}
	.u-dashboard{
		padding: 2rem;
		width: 100%;
	}
	.u-dashboard ul{
		display: flex;
		list-style-type: none;
		width: 100%;
		align-items: start;
		justify-content: space-between;
		border-bottom: 1px solid #ccc;
	}
	.u-dashboard ul li{
		width: calc(100% / 6);
		padding: .5rem;
		display: flex;
	}
	.u-dashboard ul:first-of-type li:not(li:last-of-type){
		border-right: 1px solid #ccc;
	}
	.u-dashboard ul:first-of-type li{
		font-size: 1.5rem;
	}
	@media (max-width: 720px) {
		.u-dashboard ul:first-of-type li:not(li:last-of-type){
			overflow-x: hidden;
		}
		.u-dashboard ul:first-of-type li{
			font-size: .7rem;
		}
		.u-dashboard ul li{
			font-size: .5rem;
		}
	}
</style>