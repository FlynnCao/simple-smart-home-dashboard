<script setup lang="ts">
const emits = defineEmits(['change'])
const enabled = ref(false)
const handleChange = ()=>{
  emits('change', enabled.value)
}
</script>
<template>
  <input type="radio" id="one" :value="true" v-model="enabled" @change="handleChange"/>
  <label for="one">On</label>
  <input type="radio" id="two" :value="false" v-model="enabled" @change="handleChange"/>
  <label for="two">Off</label>
</template>



<style scoped>

</style>
