<script setup>

import { ref, reactive, onMounted, watch } from 'vue'
import { db } from './data/guitarras'
import Guitarra from './components/Guitarra.vue'
import Header from './components/Header.vue'
import Footer from './components/Footer.vue'


const guitarras = ref([])
const carrito = ref([])
const guitarra  = ref({})

const CANTIDAD_MAX = 5
const CANTIDAD_MIN = 1

watch(carrito, () => {
  guardarLocalStorage()
}, {deep: true})

onMounted(() => {
  guitarras.value = db
  guitarra.value = db[3]

  const carritoLocalStorage = JSON.parse(localStorage.getItem('carrito'))
  if(carritoLocalStorage){
    carrito.value = carritoLocalStorage
  }
})

const agregarCarrito = (guitarra) => {
    const existeCarrito = carrito.value.some(product => product.id === guitarra.id);
    
    existeCarrito
      ? carrito.value = carrito.value.map(product => {
          if(product.id === guitarra.id){
            product.cantidad++
          }
          return product
        })
      : carrito.value.push({...guitarra, cantidad: 1});

    guardarLocalStorage()
  }

  const decrementarCarrito = (id) => {
    const index = carrito.value.findIndex(product => product.id === id)
    if(carrito.value[index].cantidad <= CANTIDAD_MIN) return
    carrito.value[index].cantidad--
  }

  const incrementarCarrito = (id) => {
    const index = carrito.value.findIndex(product => product.id === id)
    if(carrito.value[index].cantidad >= CANTIDAD_MAX) return
    carrito.value[index].cantidad++
  }

  const eliminarProducto = (id) => {
    carrito.value = carrito.value.filter(product => product.id !== id)
  }

  const eliminarCarrito = () => {
    carrito.value = []
  }

  const guardarLocalStorage = () => {
    localStorage.setItem('carrito', JSON.stringify(carrito.value))
  }

</script>

<template>

  <Header 
    :carrito="carrito"
    :guitarra="guitarra"
    @agregar-carrito="agregarCarrito"
    @decrementar-carrito="decrementarCarrito"
    @incrementar-carrito="incrementarCarrito"
    @eliminar-producto="eliminarProducto"
    @eliminar-carrito="eliminarCarrito"
  />

  <main class="container-xl mt-5">
    <h2 class="text-center">Nuestra Colección</h2>

    <div class="row mt-5">
      <Guitarra 
        v-for="guitarra in guitarras"
        :guitarra="guitarra"
        @agregar-carrito="agregarCarrito"
        />
    </div>
  </main>

  <Footer />

</template>
