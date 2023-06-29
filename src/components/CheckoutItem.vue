<script setup>
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
	<li>
		<img :src="img" :alt="title">
		<p>{{ title }}</p>
		<span>${{ price }} x {{ quantity }}</span>
	</li>
</template>

<style scoped>
	.ch-cart li{
		list-style-type: none;
		border-bottom: 1px solid #888;
		display: flex;flex-direction: row;
		width: 100%;
		padding: .5rem;
		align-items: center;justify-content: space-between;
	}
	.ch-cart li img{
		width: 96px;
	}
</style>