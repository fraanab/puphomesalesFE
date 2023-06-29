<script setup>
	import { ref } from 'vue'
	import axios from 'axios'

	const superuser = ref(false)
	const username = localStorage.getItem('username')
	const userId = localStorage.getItem('userId')
	const orders = ref([])
	const totalProfits = ref(0)

	const verifyAuth = async (id) => {
		try{
			const response = await axios(`http://localhost:8000/is-superuser/${id}`)
			console.log(response.data.issuper)
			superuser.value = response.data.issuper
		}catch(e){
			console.error(e.message)
		}
	}

	verifyAuth(userId)

	const getAllOrders = async () => {
		try{
			console.log('Fetching Orders...');
			const response = await axios('http://localhost:8000/order/get-all-orders/')
			console.log(response.data.orders, 'Fetching done.');

			orders.value = response.data.orders
			totalProfits.value = response.data.cards_data['totalProfits']
			console.log(totalProfits.value)
		}catch(e){
			console.error(e.message)
		}
	}

	const productTitle = ref('')
	const productDescription = ref('')
	const productThumbnail = ref('')
	const productPrice = ref(0)
	const fileInputRef = ref(null)

	const handleThumbnailChange = (e) => {
		productThumbnail.value = e.target.files[0]
	}

	const triggerFileInput = () => {
		fileInputRef.value.click()
	}

	const createProduct = async () => {
		try{
			const formData = new FormData()
			formData.append('thumbnail', productThumbnail.value)
			formData.append('name', productTitle.value)
			formData.append('description', productDescription.value)
			formData.append('price', productPrice.value)
			formData.append('userId', userId)
			const response = await axios.post('http://localhost:8000/product/new-product/', formData)
			console.log(response.data)
		}catch(e){
			console.error(e.message)
		}
	}

	if (superuser) {
		getAllOrders()
	}else{
		console.log('Page not Found... Redirecting.')
		window.location.href = '/'
	}
</script>

<template>
	<section class="a-dashboard" 
	v-if="superuser" >
		<h1>{{ username }}</h1>

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

		<div class="dash-bottom">
			
			<div class="dash-cards">
				<div class="dash-card">
					<p>Total Profits</p>
					<p>$ {{ totalProfits }}</p>
				</div>
			</div>

			<div class="new-product" enctype="multipart/form-data">
				<h2>Add a new product</h2>
				<div class="dash-new-product-fx">
					
					<div>
						<button @click="triggerFileInput" id="choose-thumbnail" class="fa-solid fa-image"></button>
						<input ref="fileInputRef" type="file" style="display: none;" @change="handleThumbnailChange" id="thumb">
					</div>
					<div>
						<label for="title">Title</label>
						<input type="text" v-model="productTitle" id="title">
					</div>

				</div>
				<label for="desc">Description</label>
				<textarea v-model="productDescription" cols="30" id="desc"></textarea>
				<label for="price">Price</label>
				<input type="number" v-model="productPrice" id="price">
				<button class="btn1" @click="createProduct">Create Product</button>
			</div>

		</div>
	</section>
	<p v-else>404</p>
</template>

<style scoped>
	#choose-thumbnail{
		font-size: 5rem;
		padding: 1rem;
		width: fit-content;
		cursor: pointer;
		border: 1px solid #ddd;
		color: #000;
	}
	#choose-thumbnail:active{
		background: linear-gradient(to bottom right, #ccc, #aaa);
	}
	.dash-bottom{
		width: 100%;
		display: flex;
		flex-direction: row-reverse;
		justify-content: space-between;
		margin-top: 1rem;
	}
	.new-product{
		padding: 1rem;
		background: #fff;
		box-shadow: 0 0 10px #ccc;
		display: flex;flex-direction: column;
		gap: .5rem;
		width: 48%;
	}
	.new-product .btn1{border: 1px solid #ddd;}
	.dash-new-product-fx{
		display: flex;justify-content: space-between;
		align-items: center;
	}
	.dash-new-product-fx div{
		display: flex;flex-direction: column;
		width: 100%;
	}
	.dash-new-product-fx div:first-of-type{
		align-items: center;
	}

	.dash-cards{
		display: flex;flex-direction: row;flex-wrap:wrap;
		width: 48%;
		align-items: start;justify-content: start;
	}
	.dash-card{
		padding: 1rem;
		/* border: 1px solid #ccc; */
		box-shadow: 0 0 10px #ccc;
		width: fit-content;
		background: #fff;
	}
	.dash-card p:first-of-type{
		font-size: 1.5rem;
		color: #555;
	}
	.dash-card p:last-of-type{
		font-size: 3rem;
		font-weight: bold;
	}
	.a-dashboard{
		padding: 3rem;
	}
	.a-dashboard ul{
		display: flex;
		list-style-type: none;
		width: 100%;
		align-items: start;
		justify-content: space-between;
		border-bottom: 1px solid #ccc;
	}
	.a-dashboard ul li{
		width: calc(100% / 6);
		padding: .5rem;
		display: flex;
	}
	.a-dashboard ul:first-of-type li:not(li:last-of-type){
		border-right: 1px solid #ccc;
	}
	.a-dashboard ul:first-of-type li{
		font-size: 1.5rem;
	}
</style>