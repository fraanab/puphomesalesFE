<script setup>
	import { defineEmits, defineProps } from 'vue'
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
		},
		quantity: {
			type: Number,
			required: true,
			default: 1,
		}
	})
</script>

<template>
	<li class="cart-item">
		<img :src="img" :alt="title">
		<h4>{{ title }}</h4>
		<div>
			<p>${{ price }}</p>
			<p>Quantity: {{ quantity }}</p>
			<div class="cart-item-actions">
				<button class="btn3" @click="$emit('remove-from-cart')">Remove</button>
			</div>
		</div>
	</li>
</template>

<style scoped>
	.cart-items{
		width: 100%;
		height: 100%;
		display: flex;flex-direction: column;gap: .5rem;
	}
	.cart-item{
		border-bottom: 1px solid #ccc;
		padding: .5rem;
		display: flex;flex-direction: row;
		align-items: center;
		justify-content: space-between;
		gap: .5rem;
		color: #000;
	}
	.cart-item img{
		width: 96px;
		background: #fff;
		border-radius: 10px;
	}
</style>