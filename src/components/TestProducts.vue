<script setup lang="ts">
import { ref, onMounted } from 'vue'
import axios from 'axios'

defineProps<{ msg: string }>()

// TypeScript interfaces for Product and Rating
interface Rating {
  rate: number;
  count: number;
}

interface Product {
  id: number;
  title: string;
  description: string;
  price: number;
  image: string;
  category?: string;
  rating: Rating;
}

// State for products and selected product
const products = ref<Product[]>([])
const selectedProduct = ref<Product | null>(null)

// Fetch products from API
const fetchProducts = async () => {
  try {
    const response = await axios.get('https://fakestoreapi.com/products')
    products.value = response.data
  } catch (error) {
    console.error('Error fetching products:', error)
  }
}

// Fetch a single product by ID
const fetchProductById = async (id: number) => {
  try {
    const response = await axios.get(`https://fakestoreapi.com/products/${id}`)
    selectedProduct.value = response.data
  } catch (error) {
    console.error('Error fetching product details:', error)
  }
}

// Load products initially
onMounted(fetchProducts)

// Function to handle product selection
const selectProduct = (id: number) => {
  fetchProductById(id)
}

// Function to go back to product list
const goBackToList = () => {
  selectedProduct.value = null
}

</script>

<template>
  <h1>{{ msg }}</h1>

  <div v-if="selectedProduct">
    <h2> Details about products</h2>
    <!-- Product Details View -->
    <div>
      <div class="flex-product">
        <img :src="selectedProduct.image" alt="Product Image" style="width: 500px;">
        <div class="p-0-3">
          <h2>{{ selectedProduct.title }}</h2>
          <p>{{ Math.floor(selectedProduct.price * 6.87) }} kr.</p>
          <p>{{ selectedProduct.description }}</p>
          <p> Category: {{ selectedProduct.category }}</p>
          <p> Rating: {{ selectedProduct.rating.rate }} based on {{ selectedProduct.rating.count }} Reviews</p>
          <button @click="goBackToList">Back to List</button>
        </div>
      </div>
    </div>
  </div>

  <div v-else>
    <h2> List of all products</h2>
    <div class="wrapper">
      <!-- Product List View -->
      <div v-for="product in products" :key="product.id" class="card">
        <h3 class="truncate">{{ product.title }}</h3>
        <img :src="product.image" alt="Product Image" style="width: 200px;">
        <div class="flex">
          <p>{{ Math.floor(product.price * 6.87) }} kr.</p>
          <button class="button" @click="selectProduct(product.id)">Køb</button>
        </div>

      </div>
    </div>
  </div>

</template>

<style scoped>
.wrapper {
  display: inline-flex;
  flex-wrap: wrap;
  justify-content: center;
}

.flex {
  display: flex;
  justify-content: space-between;
  width: 100%;
  align-items: baseline;
}

.flex-product {
  display: flex;
  justify-content: space-between;

  @media only screen and (max-width: 600px) {
    flex-wrap: wrap;
  }
}

.p-0-3 {
  padding: 0 30px;
}

.card {
  align-items: center;
  margin: 10px;
  padding: 10px;
  background-color: #fff;
  border-radius: 18px;
  box-shadow: 2px 4px 12px #00000014;
  display: flex;
  flex-direction: column;
  height: 29.4117647059rem;
  justify-content: space-between;
  overflow: hidden;
  position: relative;
  transition: all .3s cubic-bezier(0, 0, .5, 1);
  white-space: normal;
  width: 313px;
  color: black;
  padding: 26px;
}

.truncate {
  width: 100%;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}

.truncate:hover {
  white-space: wrap;
}


.card:hover {
  transform: translateY(-5px);
  box-shadow: 0 6px 20px rgba(0, 0, 0, 0.2);
}

.card img {
  object-fit: cover;
  margin-bottom: 10px;
}

.button {
  border-radius: 20px;
  border: 1px solid transparent;
  padding: 0 1.2em;
  font-size: 1em;
  font-weight: 500;
  font-family: inherit;
  background-color: #1a1a1a;
  cursor: pointer;
  transition: border-color 0.25s;
  height: 36px;
}

.button:hover {
  background-color: #0056b3;
}
</style>