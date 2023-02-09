<script setup lang="ts">
const emits = defineEmits(['change'])
const freq = ref(5)
const handleChange = () => {
  console.log('freq.value', freq.value)
  if (freq.value == 0) {
    console.log("Detect stopped!")
  } else {
    console.log(`Detect every ${freq.value} second(s).`)
  }
  emits('change', freq.value)
}
</script>


<template>
  <select v-model="freq" @change="handleChange">
    <option :value="0">Stop</option>
    <option :value="1">Per Second</option>
    <option :value="5">Every 5 Seconds</option>
    <option :value="10">Every 10 Seconds</option>
  </select>

</template>
