<script setup>
import Calculator from './components/Calculator.vue'
</script>

<template>
  <div class="main-bg">
    <Calculator />
  </div>
</template>

<style scoped>
.main-bg {
  min-height: 100vh;
  height: 100vh;
  width: 100vw;
  display: flex;
  justify-content: center;
  align-items: center;
  background: linear-gradient(120deg, #FFA63D 0% 55%, #4B3950 55% 100%);
  position: fixed;
  top: 0;
  left: 0;
  overflow: hidden;
}
</style>
