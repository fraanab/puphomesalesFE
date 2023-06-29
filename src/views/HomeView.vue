<script setup>
  import Product from '@/components/Product.vue'
  import Cart from '@/components/Cart.vue'
  import axios from 'axios'
  import { ref, onMounted, provide } from 'vue'

  let products = ref([])
  let cartProducts = ref([])

  const getCartProducts = () => {
    const storedCartItems = JSON.parse(localStorage.getItem('cartItems')) || []
    cartProducts.value = storedCartItems
  }

  const getProducts = async () => {
    try{
      const response = await axios('http://localhost:8000/product/get-products/')
      console.log(response.data)
      products.value = response.data
    }catch(e){
      console.error(e.message);
    }
  }

  const addToCart = (product) => {
    const cartItems = JSON.parse(localStorage.getItem('cartItems')) || []

    const existingProduct = cartItems.findIndex((item) => item.id === product.id)
    if (existingProduct !== -1) {
      let previousQuantity = cartItems[existingProduct].quantity
      cartItems[existingProduct].quantity++
      cartItems[existingProduct].price = (cartItems[existingProduct].price / previousQuantity) * cartItems[existingProduct].quantity
    }else{
      product.quantity = 1
      cartItems.push(product)
    }

    localStorage.setItem('cartItems', JSON.stringify(cartItems))
    getCartProducts()
    console.log(cartItems)
  }

  const deleteProduct = async (id) => {
    const userId = localStorage.getItem('userId')
    try{
      const confirmed = window.confirm('Are you sure you want to delete this product?')
      if (confirmed) {
        const response = await axios.delete(`http://localhost:8000/product/delete-product/${id}/`, {
          params: {
            userId: userId
          }
        })
        console.log(response.data.message)

        const productIndex = products.value.findIndex((product) => product.id === id)
        if (productIndex !== -1) {
          products.value.splice(productIndex, 1)
        }
      }else{
        console.log('Deletion cancelled')
      }
    }catch(e){
      console.log(e.message)
    }
  }

  getProducts()

  provide('getCartProducts', getCartProducts)
  provide('cartProducts', cartProducts)
</script>

<template>
  <header>
    <div class="header-a">
      <h1><span><i class="fa-solid fa-paw"></i></span> Get The <span>Perfect</span> Home For Your Pupper <span><i class="fa-solid fa-paw"></i></span></h1>
      <a href="#products-grid">Show me</a>
    </div>

    <div class="header-b">
      <img src="@/assets/house.webp" alt="Header house">
    </div>
  </header>


  <section id="products-grid">
    <h2>Wooden House Kits</h2>
    <p>Easy to assemble, and come with all the materials you need.</p>
    <div class="products-grid" v-if="products.length >= 1">

      <Product v-for="product in products" :key="product.id" 
        :img="'http://localhost:8000' + product.thumbnail"
        :title="product.name"
        :price="product.price"
        @add-to-cart="addToCart(product)"
        @delete-product="deleteProduct(product.id)"
      />

    </div>
    <span v-else>Soon to come...</span>
  </section>



  <section class="faq" id="faq">
    <h2>FAQ</h2>
    <ul>
      <li>
        <question>How <span>long</span> does it <span>take</span> to assemble a wooden dog house kit?</question>
        <answer>Assembly times vary depending on the size and complexity of the kit, but most can be assembled in a <span>few hours</span> or less.</answer>
      </li>

      <li>
        <question>What <span>tools</span> do I <span>need</span> to assemble a wooden dog house kit?</question>
        <answer>Most kits can be assembled with <span>basic hand tools</span>, such as a hammer, screwdriver, and saw.</answer>
      </li>

      <li>
        <question>Are the wooden dog house kits <span>weatherproof</span>?</question>
        <answer><span>Yes</span>, all of our wooden dog house kits are made with <span>weather-resistant materials</span> and are designed to withstand the elements.</answer>
      </li>
    </ul>
  </section>


  <section class="contact">
    <h3>Contact Information</h3>
    <p>Customer Support Email: <span>support@woofhaus.com</span></p>
    <p>Customer Support Phone Number: <span>1-800-123-4567</span></p>
    <p>Physical Address: <span>123 Main Street, Anytown USA</span></p>
    <img src="@/assets/dog4.png" alt="contact image">
  </section>

  <Cart />

</template>

<style scoped>
  .contact{
    width: 100%;
    display: flex;flex-direction: column;
    gap: .5rem;
    align-items: center;

    background: linear-gradient(to top, var(--a), #f1f1f1);
  }
  .contact h3{font-size: 2rem;font-family: 'lemon', sans-serif;}
  .contact p span{font-weight: bold;}
  .contact img{
    width: 300px;
    animation: bark 1s alternate infinite;
  }
  .faq{
    width: 100%;
    display: flex;flex-direction: column;
    gap: 2rem;
    padding: 3rem;
  }
  .faq ul{
    list-style-type: none;
    display: flex;flex-direction: column;
    gap: 2rem;
    padding: 1rem 0;
    border-top: 1px solid var(--a);
    border-bottom: 1px solid var(--a);
  }
  .faq ul li{
    display: flex;flex-direction: row;
    flex-wrap:wrap;
    width: 100%;
    justify-content: space-between;
  }
  .faq question{
    height: 100%;
    width: 50%;
    font-size: 4rem;
    margin-bottom: 4rem;
  }
  .faq answer{
    width: 50%;
    height: 100%;
    font-size: 2rem;
  }
  .faq h2{
    font-family: 'lemon', sans-serif;
    font-size: 2.5rem;
  }
  #products-grid{
    display: flex;align-items: center;justify-content: center;
    flex-direction: column;
    width: 100%;
    min-height: 75vh;
  }
  #products-grid h2{
    font-family: 'lemon', sans-serif;
    font-size: 2.5rem;
  }
  #products-grid p{
    font-size: 1.25rem;
    color: #555;
  }


  header{
    width: 100%;
    min-height: 100vh;
    overflow-x: hidden;
    display: flex;align-items: center;justify-content: space-around;
    padding: 3rem 0;

  }
  .header-a{
    width: 50vw;
  }
  .header-a h1{
    font-family: 'lemon', sans-serif;
    font-size: 6rem;
  }
  .header-a a{
    text-decoration: none;
    /* width: fit-content; */
    width: 100%;
    display: flex;
    justify-content: center;
    background: #aaa;
    padding: 1rem 3rem;
    font-family: 'lemon', sans-serif;
    color: var(--a);background: #000;
    margin: 0 auto;
  }
  .header-a a:hover{
    background: var(--a);
    color: #000;
  }

  header img{
    width: 30vw;
    filter: drop-shadow(5px 15px #ccc);
  }

  .products-grid{
    display: flex;flex-direction: row;
    flex-wrap:wrap;
    gap: 1rem;
    padding: 1rem;
    width: 100%;
    justify-content: center;
  }
  .product{
    padding: .5rem;
    display: flex;flex-direction: column;
    gap: .5rem;
    box-shadow: 0 0 10px #ccc;
    background: #fff;
  }
  .product:hover{
    transform: scale(1.05);
    box-shadow: 0 0 8px #f1f1f1;
  }

  @media (max-width: 900px) {
    header{
      flex-direction: column-reverse;
    }
    .header-a{
      width: 100%;
      padding: 1rem;
    }
    .header-b{
      display: none;
    }
    .faq answer{
      width: 45%;
    }
  }

  @media (max-width: 720px) {
    header{
      height: 100vh;
    }
    .header-a{
      padding: 0 1rem;
      display: flex;
      flex-direction: column;
      align-items: center;justify-content: space-evenly;
      height: 100%;
    }
    .header-a h1{
      font-family: 'lemon', sans-serif;
      font-size: 4rem;
    }
    #products-grid h2{text-align: center;}
    #products-grid p{padding: 1rem 2rem;}
    .faq{
      padding: 3rem 0;
    }
    .faq ul li{
      flex-direction: column;
    }
    .faq question{
      width: 100%;
      font-size: 2rem;
      padding: 0 3rem;
      margin-bottom: 1rem;
    }
    .faq answer{
      background: var(--a);
      color: #f1f1f1;
      padding: 1rem 3rem;
      width: 100%;
      font-size: 2rem;
    }
    .faq answer span{
      color: #fff;
      font-weight: bold;
      border-bottom: 2px solid #fff;
    }
    .faq h2{padding: 0 3rem;}
    .faq ul{gap: 1rem;border-bottom: none;}
  }
</style>