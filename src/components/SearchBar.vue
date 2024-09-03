<template>
  <div class="input-group mb-3">
    <input 
      type="text" 
      class="form-control" 
      placeholder="Search for products..." 
      v-model="searchQuery"
    />
    <div class="input-group-append">
      <button class="btn btn-outline-secondary" @click="search">Search</button>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      searchQuery: ''
    };
  },
  methods: {
    search() {
      this.$emit('search', this.searchQuery);
    }
  }
};
</script>
