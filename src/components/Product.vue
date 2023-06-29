<script setup>
	import { ref, defineProps } from 'vue'
	import axios from 'axios'

	const superuser = ref(false)
	const userId = localStorage.getItem('userId')

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

	defineProps({
		img: {
			type: String,
			required: true,
		},
		title: {
			type: String,
			required: true,
		},
		price: {
			type: String,
			required: true,
		}
	})
</script>

<template>
	<div class="product">
    <img :src="img" :alt="title">
    <p>{{ title }}</p>
    <span>$ {{ price }}</span>
    <button class="btn1" @click="$emit('addToCart')">Add to Cart</button>
    <button v-if="superuser" class="btn3"
     @click="$emit('deleteProduct')" >delete product</button>
  </div>
</template>

<style scoped>
	
  .product{
    padding: .5rem;
    display: flex;flex-direction: column;
    gap: .5rem;
    box-shadow: 0 0 10px #ccc;
    background: #fff;
    width: 300px;
    height: 430px;
    justify-content: space-between;
  }
  .product:hover{
    transform: scale(1.05);
    box-shadow: 0 0 8px #f1f1f1;
  }
  .product img{
  	width: 290px;
  	height: 70%;
  	filter: drop-shadow(-3px 3px #ccc);
  }
  .product p:first-of-type{
  	font-weight: bold;
  }
</style>