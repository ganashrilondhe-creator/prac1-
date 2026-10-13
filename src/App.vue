
<template>
  <div id="app-container">
    <Login />
  </div>
</template>

<script>
// Import the Login component
import Login from './components/Login.vue'

export default {
  name: 'App',
  components: {
    Login
  }
}
</script>

<style>

#app-container {
  min-height: 100vh;
  display: flex;
  justify-content: center;
  align-items: flex-start;
  padding: 2rem;
  box-sizing: border-box;
}
</style>



