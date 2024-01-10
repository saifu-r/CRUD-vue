<template>
  <div>
    <header><h2>Create Read Update Delete</h2></header>
    <toggle-components></toggle-components>
    
  </div>

</template>

<script lang="ts">
import { defineComponent } from 'vue';
import ToggleComponents from './components/Resources/ToggleComponents.vue';


export default defineComponent({
  name: 'App',
  components: {ToggleComponents}
});
</script>

<style>
@import url('https://fonts.googleapis.com/css2?family=Inter&display=swap');



body{
  margin: 0;
  font-family: 'Inter', sans-serif;
}

header {
  background-color: #3a0061;
  color: white;

  padding: 1rem;
  
}

header h2 {
  margin: 0;
}

</style>
