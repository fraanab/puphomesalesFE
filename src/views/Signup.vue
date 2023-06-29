<script setup>
	import { RouterLink } from 'vue-router'
	import { ref } from 'vue'
	import axios from 'axios'

	const username = ref('')
	const email = ref('')
	const password1 = ref('')
	const password2 = ref('')
	const msg = ref('')

	const register = async () => {
		if (password1.value !== password2.value){
			msg.value = 'Passwords do not match'
			return
		}
		if (email.value === '' || password1.value === '' || password2.value === '' || username.value === '' || password1.value.length < 8){
			msg.value = 'Fields must not be empty'
			return
		}
		try{
			const response = await axios.post('https://puphomessalesbe.onrender.com/signup/', {
				'username': username.value,
				'email': email.value,
				'password': password1.value,
			})
			msg.value = response.data.success
			window.location.href = '/login'
		}catch(e){
			console.error(e.message)
			msg.value = e.message
		}
	}
</script>

<style scoped>
	section{
		width: 100;
		/* min-height: 80vh; */
		/* height: 81.7vh; */
		height: 100%;
		display: flex;justify-content: space-between;

	}
	.auth-form a{
		color: var(--a);
	}
	.auth-form h2{
		width: 100%;text-align: center;
		font-size: 4rem;
		font-family: 'lemon', sans-serif;
	}
	.auth-form{
	  width: 40%;
	  height: 80vh;
	  padding: 1rem;
	  display: flex;flex-direction: column;
	  gap: .5rem;
	  box-shadow: 10px 0 10px #ccc;
	  background: #fff;
	}
	.signup-img{
		width: 60%;
		max-height: 80vh;
		height: 100%;
		overflow: hidden;
		display: flex;align-items: center;justify-content: center;
	}
	.signup-img img{
		/* height:80vh; */
		filter: drop-shadow(80px 55px #888);
	}
	@media (max-width: 720px) {
		section{
			flex-direction: column-reverse;
		}
		.auth-form{
			width: 100%;
		}
		.signup-img{
			width: 100%;
			max-height: 30vh;
		}
		.signup-img img{
			width: 100%;
			transform: translateY(80px)
		}
	}
</style>

<template>
	<section>
		<div class="auth-form">
			<h2>Sign Up</h2>
			
			<label for="username">Username</label>
			<input v-model="username" type="text" id="username" placeholder="vue">
			<label for="email">Email</label>
			<input v-model="email" type="email" id="email" placeholder="email@vue.com">
			<label for="password1">Password</label>
			<input v-model="password1" type="password" id="password1" placeholder="********">
			<label for="password2">Repeat Password</label>
			<input v-model="password2" type="password" id="password2" placeholder="********">
			<RouterLink to="/login">Already have an account?</RouterLink>
			<button class="btn1" @click="register">Sign Up</button>

			<p class="msg">{{ msg }}</p>

		</div>

		<div class="signup-img">
			<img src="@/assets/dog2.png" alt="signup dog">
		</div>
	</section>
</template>