<template>
  <div class="product-cart">
    <div v-if="cart.length">
      <ul class="list-group mb-3">
        <li v-for="item in cart" :key="item.product.id" class="list-group-item">
          <div class="d-flex justify-content-between align-items-center">
            <div>
              <strong>{{ item.product.name }}</strong> - {{ item.product.price }} ₽ x {{ item.quantity }}
            </div>
          </div>
          <div class="d-flex justify-content-between align-items-center">
            <div class="mt-2 button-group">
            <button
              class="btn btn-outline-primary btn-sm"
              @click="increaseQuantity(item.product.id)"
            >
              +
            </button>
            <button
              class="btn btn-outline-primary btn-sm"
              @click="decreaseQuantity(item.product.id)"
            >
              -
            </button>
          </div>
            <button
              class="btn btn-outline-danger btn-sm"
              @click="removeFromCart(item.product.id)"
            >
              Удалить
            </button>
          </div>
        </li>
      </ul>
      <!-- Блок с итоговой суммой и кнопками -->
      <div class="cart-footer">
        <p>
          <strong>Total: {{ total }} ₽</strong>
        </p>
        <div class="d-flex justify-content-between">
          <button class="btn btn-success mb-2" @click="placeOrder">Оформить заказ</button>
          <button class="btn btn-danger mb-2" @click="clearCart">Очистить корзину</button>
        </div>
      </div>
    </div>
    <div v-else>
      <p>Корзина пуста</p>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    cart: Array
  },
  computed: {
    total() {
      return this.cart.reduce((sum, item) => sum + item.product.price * item.quantity, 0)
    }
  },
  methods: {
    increaseQuantity(id) {
      this.$emit('update-quantity', { id, amount: 1 })
    },
    decreaseQuantity(id) {
      this.$emit('update-quantity', { id, amount: -1 })
    },
    removeFromCart(id) {
      this.$emit('remove-from-cart', id)
    },
    clearCart() {
      this.$emit('clear-cart')
    },
    placeOrder() {
      this.$emit('place-order') // Вызов события для оформления заказа
      alert(`Ваш заказ на сумму ${this.total} ₽ успешно оформлен!`)
      this.$emit('clear-cart') // Очистка корзины после оформления заказа
    }
  }
}
</script>

<style scoped>
.product-cart {
  padding: 20px;
}

.button-group {
  display: flex;
  gap: 10px;
}

.button-group button {
  width: 40px;
  height: 30px;
  padding: 0;
}

.cart-footer {
  border-top: 1px solid #ddd;
  padding-top: 15px;
  margin-top: 15px;
}

.cart-footer p {
  margin-bottom: 10px;
}

.cart-footer .d-flex {
  gap: 10px; /* Добавляем отступы между кнопками */
}

.list-group {
  max-height: 600px;
  overflow-y: auto;
  padding: 1rem;
}
</style>
