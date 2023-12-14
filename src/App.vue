<!-- App.vue -->
<template>
  <div id="app" class="bg-gray-200 min-h-screen flex items-center justify-center">
    <Weather />
  </div>
</template>

<script setup>
import Weather from './components/Weather.vue';

</script>

<script>


export default {
  name: 'App',
  components: {
    Weather,
  },
};
</script>

<style>
/* Add global styles or Tailwind CSS classes here */

body {
  font-family: 'Arial', sans-serif;
}

#app {
  display: flex;
  align-items: center;
  justify-content: center;
  height: 100vh;
}

</style>
