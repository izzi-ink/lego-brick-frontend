<template>
  <div id="app">
    <!-- Navigation/Header -->
    <nav class="navbar navbar-dark bg-primary">
      <div class="container">
        <span class="navbar-brand h1">🧱 LEGO Brick System</span>
        <small class="text-light">Business Analyst Service Mapper</small>
      </div>
    </nav>
    
    <!-- Add new brick form -->
    <BrickForm @brick-created="refreshBrickList" />
    
    <!-- Divider -->
    <hr class="my-5">
    
    <!-- Show all bricks -->
    <BrickList ref="brickList" />
  </div>
</template>

<script>
import BrickForm from './components/BrickForm.vue'
import BrickList from './components/BrickList.vue'

export default {
  name: 'App',
  components: {
    BrickForm,
    BrickList
  },
  methods: {
    refreshBrickList() {
      // When a new brick is created, refresh the list
      this.$refs.brickList.fetchBricks()
    }
  }
}
</script>

<style>
body {
  background-color: #f8f9fa;
}
</style>