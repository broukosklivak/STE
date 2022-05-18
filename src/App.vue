<template>
  <router-view/>
  <Footer />
</template>

<script>
import Footer from './components/Footer.vue'

export default {
  name: 'App',
  components: {
    Footer
  }  
}
</script>

<style>
  @import url('https://fonts.googleapis.com/css2?family=Archivo+Narrow&display=swap');

  body{
    font-family: 'Archivo Narrow', sans-serif;
  }
</style>