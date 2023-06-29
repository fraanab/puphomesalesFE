<script setup>
	import { RouterLink } from 'vue-router'
	import { ref } from 'vue'
	import axios from 'axios'

	const username = ref('')
	const password = ref('')
	const msg = ref('')

	const login = async () => {
		if (username.value === '' || password.value.length < 8){
			msg.value = 'Fields must not be empty'
			return
		}
		try{
			const response = await axios.post('http://localhost:8000/login/', {
				'username': username.value,
				'password': password.value,
			})
			localStorage.setItem('username', response.data.user_data.username)
			localStorage.setItem('userId', response.data.user_data.id)
			localStorage.setItem('email', response.data.user_data.email)

			console.log(response.data.user_data.username)

			msg.value = 'Logged in'

			window.location.href = '/'
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
		transform: translateY(15%)
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
		}
	}
</style>

<template>
	<section>
		<div class="auth-form">
			<h2>Log In</h2>
			<label for="username">Username</label>
			<input v-model="username" type="text" id="username" placeholder="vue">
			<label for="password">Password</label>
			<input v-model="password" type="password" id="password" placeholder="********">
			<RouterLink to="signup">Create your account</RouterLink>
			<button @click="login" class="btn1">Log In</button>

			<p class="msg">{{ msg }}</p>

		</div>

		<div class="signup-img">
			<img src="@/assets/dog1.png" alt="signup dog">
		</div>
	</section>
</template>