<template>
  <div id="app" class="container d-flex flex-column align-items-center">
    <div class="d-flex justify-content-between align-items-center my-4 w-100">
      <h1 class="mb-0">Магазин на Vue.js</h1>
      <div class="d-flex align-items-center">
        <button class="btn btn-primary d-flex align-items-center" @click="toggleCartModal">
          <p class="mb-0 me-2 p-0">Открыть корзину</p>
          <span class="badge bg-success p-2 pb-1">{{ cartTotal }} ₽</span>
        </button>
      </div>
    </div>

    <!-- Toast уведомление -->
    <div class="toast-container position-fixed bottom-0 end-0 p-3">
      <div
        id="orderToast"
        class="toast"
        role="alert"
        aria-live="assertive"
        aria-atomic="true"
        data-bs-autohide="true"
        data-bs-delay="3000"
      >
        <div class="toast-header">
          <strong class="me-auto">Заказ оформлен</strong>
          <small>Только что</small>
          <button type="button" class="btn-close" @click="hideToast"></button>
        </div>
        <div class="toast-body">Ваш заказ был успешно оформлен!</div>
      </div>
    </div>

    <div class="mb-4 w-100">
      <SearchBar @search="searchProducts" />
    </div>
    <ProductList :products="filteredProducts" @add-to-cart="addToCart" />
    <ModalWindow :visible="isCartVisible" @close="toggleCartModal">
      <ProductCart
        :cart="cart"
        @remove-from-cart="removeFromCart"
        @update-quantity="updateQuantity"
        @clear-cart="clearCart"
        @place-order="placeOrder"
      />
    </ModalWindow>
  </div>
</template>

<script>
import { Toast } from 'bootstrap'

import ProductList from './components/ProductList.vue'
import ProductCart from './components/ProductCart.vue'
import SearchBar from './components/SearchBar.vue'
import ModalWindow from './components/ModalWindow.vue'

export default {
  components: {
    ProductList,
    ProductCart,
    SearchBar,
    ModalWindow
  },
  data() {
    return {
      products: [],
      searchQuery: '',
      cart: [],
      isCartVisible: false
    }
  },
  computed: {
    filteredProducts() {
      if (this.searchQuery) {
        return this.products.filter((product) =>
          product.name.toLowerCase().includes(this.searchQuery.toLowerCase())
        )
      } else {
        return this.products
      }
    },
    cartTotal() {
      return this.cart.reduce((sum, item) => sum + item.product.price * item.quantity, 0)
    }
  },
  methods: {
    async fetchProducts() {
      try {
        const response = await fetch('/products.json')
        this.products = await response.json()
      } catch (error) {
        console.error('Не получилось загрузить список продуктов:', error)
      }
    },

    addToCart(product) {
      const cartItem = this.cart.find((item) => item.product.id === product.id)
      if (cartItem) {
        cartItem.quantity++
      } else {
        this.cart.push({ product, quantity: 1 })
      }
      this.saveOrderToStorage() // Сохранение изменений корзины
    },

    removeFromCart(id) {
      this.cart = this.cart.filter((item) => item.product.id !== id)
      this.saveOrderToStorage() // Сохранение изменений корзины
    },

    updateQuantity({ id, amount }) {
      const cartItem = this.cart.find((item) => item.product.id === id)
      if (cartItem) {
        cartItem.quantity += amount
        if (cartItem.quantity <= 0) {
          this.removeFromCart(id)
        }
      }
      this.saveOrderToStorage() // Сохранение изменений корзины
    },

    clearCart() {
      this.cart = []
      this.saveOrderToStorage() // Сохранение изменений корзины
    },

    searchProducts(query) {
      this.searchQuery = query
    },

    toggleCartModal() {
      this.isCartVisible = !this.isCartVisible
    },

    saveOrderToStorage() {
      const orderDetails = this.cart.map((item) => ({
        name: item.product.name,
        quantity: item.quantity,
        price: item.product.price
      }))
      const total = this.cart.reduce((sum, item) => sum + item.product.price * item.quantity, 0)
      const orderData = {
        orderDetails,
        total
      }
      localStorage.setItem('order', JSON.stringify(orderData))
    },

    loadOrderFromStorage() {
      const orderData = JSON.parse(localStorage.getItem('order'))
      if (orderData) {
        this.cart = orderData.orderDetails.map((item) => ({
          product: {
            name: item.name,
            price: item.price
          },
          quantity: item.quantity
        }))
      }
    },

    placeOrder() {
      const orderDetails = this.cart.map((item) => ({
        name: item.product.name,
        quantity: item.quantity,
        price: item.product.price
      }))
      const total = this.cart.reduce((sum, item) => sum + item.product.price * item.quantity, 0)
      const orderData = {
        orderDetails,
        total
      }
      this.saveOrderToStorage() // Сохраняем заказ в localStorage
      this.showToast() // Показать уведомление при оформлении заказа
      console.log(orderData) // Выводим данные заказа в консоль для проверки
    },

    showToast() {
      const toastElement = document.getElementById('orderToast')
      const toast = new Toast(toastElement) // Используем Toast, импортированный из Bootstrap
      toast.show()
    },

    hideToast() {
      const toastElement = document.getElementById('orderToast')
      const toast = new Toast(toastElement)
      toast.hide()
    }
  },
  created() {
    this.fetchProducts()
    this.loadOrderFromStorage() // Восстановление заказа при загрузке страницы
  }
}
</script>

<style>
#app {
  max-width: 100%;
  margin: 0 auto;
  padding: 20px;
  display: flex;
  flex-direction: column;
  align-items: center;
}

.w-100 {
  width: 100%;
}

.container {
  display: flex;
  flex-direction: column;
  align-items: center;
}

.toast-container {
  z-index: 1060;
}

.toast {
  background-color: #28a745;
  color: white;
}
</style>
