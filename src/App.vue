<script setup>
import VSplitDateSelect from './components/VSplitDateSelect.vue'
</script>

<template>
  <header>
    
  </header>

  <main>
    <div class="datebox">
      <div class="text">Inserisci una data: </div>
      <br>
      
        <VSplitDateSelect start="" @result="getResult"/>
        <br><div class="text">{{ chosen_date }}</div>
    </div>
  </main>
</template>

<script>
export default {
  data(){
    return{
      chosen_date: null
    }
  },

  methods: {
    getResult(value) {
      if(value != null)
        this.chosen_date = value; 
      else this.chosen_date = "null";
    }
  }
}
</script>

<style scoped>

.datebox{
  position: absolute;
  left: 50%;
  transform: translate(-50%);
  width: auto;
  min-width: 400px !important;
}

.text{
  text-align: center;
}

</style>
