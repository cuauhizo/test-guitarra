<template>
  <Header
    :carrito="carrito"
    :guitarra="guitarra"
    @incrementar-cantidad="incrementarCantidad"
    @decrementar-cantidad="decrementarCantidad"
    @agregar-carrito="agregarCarrito"
    @eliminar-producto="eliminarProducto"
    @vaciar-carrito="vaciarCarrito"
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

<script setup>
import { ref, onMounted, watch } from "vue";
import { db } from "./data/guitarras";
import Header from "./components/Header.vue";
import Guitarra from "./components/Guitarra.vue";
import Footer from "./components/Footer.vue";

const guitarras = ref([]);
const carrito = ref([]);
const guitarra = ref({});

onMounted(() => {
  guitarras.value = db;
  guitarra.value = db[3];

  const carritoStorage = localStorage.getItem("carrito");
  if (carritoStorage) {
    carrito.value = JSON.parse(carritoStorage);
  }
});

watch(carrito, () => {
    // console.log("algo cambio");
    guardarLocalStorage();
    }, {
      deep: true,
  });

const guardarLocalStorage = () => {
  localStorage.setItem("carrito", JSON.stringify(carrito.value));
};

const agregarCarrito = (guitarra) => {  
  const existeCarrito = carrito.value.findIndex(
    (producto) => producto.id === guitarra.id
  );
  // console.log(existeCarrito);
  // console.log(guitarra);
  if (existeCarrito >= 0) {
    carrito.value[existeCarrito].cantidad++;
  } else {
    guitarra.cantidad = 1;
    carrito.value.push(guitarra);
  }
};

const incrementarCantidad = (id) => {
  // console.log(id);
  const index = carrito.value.findIndex((producto) => producto.id === id);
  if (carrito.value[index].cantidad >= 5) return;
  carrito.value[index].cantidad++;
};

const decrementarCantidad = (id) => {
  // console.log(id);
  const index = carrito.value.findIndex((producto) => producto.id === id);
  if (carrito.value[index].cantidad <= 1) return;
  carrito.value[index].cantidad--;
};

const eliminarProducto = (id) => {
  console.log(id);
  carrito.value = carrito.value.filter((producto) => producto.id !== id);
};

const vaciarCarrito = () => {
  // console.log(carrito);
  // carrito.value = carrito.value.splice(0, carrito.value.length)
  carrito.value = [];
};

</script>

<style scoped></style>
