<script setup>
  import { RouterLink, RouterView } from 'vue-router'
  import { ref } from 'vue'
  import axios from 'axios'

  let session = ref(false)

  if (localStorage.getItem('userId')){
    session.value = true
  }

  const logout = async () => {
    try{
      const response = await axios.post('https://puphomessalesbe.onrender.com/logout/')

      localStorage.removeItem('username')
      localStorage.removeItem('userId')
      localStorage.removeItem('email')
      session.value = false
      console.log('Logged out', `user id ${localStorage.getItem('userId')}`, session.value)
      window.location.href = '/'
    }catch(e){
      console.error(e.message)
    }
  }

  let isNavOpen = ref(false)
  const toggleNav = () => {
    isNavOpen.value = !isNavOpen.value
  }
</script>

<template>
  <nav id="navBar" v-if="isNavOpen">
    <RouterLink to="/">Home</RouterLink>
    <a href="/#products-grid">Shop</a>
    <a href="/#faq">About</a>
    <div v-if="session">
      <RouterLink to="/checkout">Cart</RouterLink>
      <RouterLink to="/user-page">My Orders</RouterLink>
      <button class="logout" @click="logout">Log Out</button>
    </div>
    <RouterLink to="/login" v-else>Sign In</RouterLink>
  </nav>
  <button @click="toggleNav" id="toggleNav" class="fa-solid fa-bars"></button>

  <main>
    <RouterView />
  </main>

  <footer>
    <RouterLink to="/">Home</RouterLink>
    <a href="#faq">About</a>
    <RouterLink v-if="session" to="/checkout">Cart</RouterLink>
    <RouterLink v-if="session" to="/user-page">My Orders</RouterLink>
    <button v-if="session" @click="logout">Log Out</button>
    <RouterLink v-if="!session" to="/login">Sign In</RouterLink>
  </footer>
</template>

<style scoped>
  /* @import '@/assets/navbar.css' */
  nav{
    display: flex;
    position: fixed;
    top: 0;left: 0;
    background: rgba(0,0,0,.9);
    z-index: 20;
    height: 100%;
    width: 100%;
    flex-direction: column;
    justify-content: center;align-items: center;
    flex-wrap: nowrap;
    overflow: hidden;
  }
  nav div{
    display: flex;flex-direction: column;
    align-items: center;justify-content: center;
    height: fit-content;
    width: 100%;
    gap: 0;
  }
  nav a,
  nav div a{
    color: #fff;
    text-decoration: none;
    display: flex;align-items: center;justify-content: center;
    font-size: 1.5rem;
    font-weight: bold;
    font-style: italic;
    height: fit-content;
    padding: 1rem 0;
    width: 100%;
    margin: 0;
  }
  .logout{
    color: #fff;
    background: transparent;
    cursor: pointer;
    font-size: 1rem;
    border: none;
    font-size: 1.5rem;
    font-weight: bold;
    font-style: italic;

    height: fit-content;
    width: 100%;
    padding: 1rem 0;
  }
  .logout:hover,
  nav a:hover{
    background: #f1f1f1;
    color: var(--a);
  }
  #toggleNav{
    display: block;
    position: fixed;
    top: 1.5rem;
    right: 1.2rem;
    z-index: 21;
    padding: 15px;
    border-radius: 100px;
    font-size: 1.5rem;
    /* color: #fff;background: #000; */
    color: #000;background: #fff;
    box-shadow: 0 0 15px #888;
    border:none;
    cursor: pointer;
  }
  #toggleNav:hover{
    background: var(--a);color: #fff;
  }
</style>

<style scoped>
  main{
    width: 100%;
    min-height: 80vh;
  }
  footer{
    width: 100%;
    padding: 4rem 1.5rem;
    background: #000;
    display: flex;flex-direction: row;
    flex-wrap:wrap;
    gap: .5rem;
  }
  footer button,
  footer a{
    color: #fff;
    text-decoration: none;
  }
  footer button{
    background: transparent;
    border:none;
    font-size: 1rem;
    cursor: pointer;
  }
</style>
