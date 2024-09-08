<template>
  <div class="row">
    <div class="col-md-3 mb-3" v-for="product in products" :key="product.id">
      <div class="card mb-2 shadow-sm">
        <img :src="product.imageUrl" class="card-img-top" alt="Product image" />
        <div class="card-body d-flex flex-column">
          <h5 class="card-title">{{ product.name }}</h5>
          <p class="card-text">{{ product.description }}</p>
          <p class="card-text mt-auto">
            <strong>{{ product.price }} ₽</strong>
          </p>
          <button class="btn btn-success mt-2" @click="addToCart(product)">
            Добавить в корзину
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    products: Array
  },
  methods: {
    addToCart(product) {
      this.$emit('add-to-cart', product)
    }
  }
}
</script>

<style scoped>
.card {
  height: 100%;
}

.card-img-top {
  width: 100%;
  height: auto;
  max-height: 200px;
  object-fit: cover;
}
</style>
