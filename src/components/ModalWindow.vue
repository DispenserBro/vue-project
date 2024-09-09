<template>
  <div v-if="visible" class="modal-backdrop" @click.self="closeModal">
    <div class="modal-content">
      <div class="modal-header d-flex justify-content-between">
        <h5 class="modal-title">Корзина</h5>
        <button type="button" class="btn-close bg-danger p-2" @click="closeModal"></button>
      </div>
      <div class="modal-body">
        <slot></slot>
        <!-- Контент модального окна передается через слот -->
      </div>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    visible: Boolean
  },
  methods: {
    closeModal() {
      this.$emit('close') // Закрывает модальное окно при клике
    }
  }
}
</script>

<style scoped>
.modal-backdrop {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.5);
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 1050;
}

.modal-content {
  background-color: #fff;
  border-radius: 5px;
  width: 500px;
  max-width: 90%;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
}

.modal-header {
  padding: 1rem;
  border-bottom: 1px solid #e5e5e5;
}
</style>
