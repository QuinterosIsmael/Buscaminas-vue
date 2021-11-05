<template>
  <div class="principal">
      <tablero/>

      
  </div>
</template>

<script>
import Tablero from './components/Tablero.vue'

export default {
  name: 'App',
  components: {
    Tablero
  }
}
</script>

<style>
.principal{
    display: grid;
    justify-content: center;

}
</style>
