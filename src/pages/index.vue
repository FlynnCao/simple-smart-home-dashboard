<script setup lang="ts">
import axios from 'axios'
const user = useUserStore()
const name = $ref(user.savedName)
const router = useRouter()
let timer = null // Store the interval index
const lastUpdateTime = ref(new Date())
const status = reactive({
  humidity: 0.0,
  temperature: 0.0,
  rain: false,
  smoke2: false,
  smoke7: false,
  dust: false,
})
const go = () => {
  if (name)
    router.push(`/hi/${encodeURIComponent(name)}`)
}

const { t } = useI18n()

const getDataFromJSON = () => {
  axios.get('/data.json').then((res) => {
    console.log('res :>> ', res)
    console.log('res.data. :>> ', res.data.Gas2)
    lastUpdateTime.value = new Date()
    processData(res.data)
  }).catch((error) => {
    // alert('READ JSON FILE DATA ERROR')
    console.error(error)
  })
}

const processData = (data) => {
  const humid = data?.Humidity?.slice(9, 15)
  console.log(humid)
  status.humidity = humid
  const temp = data?.Temp?.slice(13, 18)
  status.temperature = temp
  console.log('temp :>> ', temp)
  const rain = data?.Rain?.length !== 9
  status.rain = rain
  const gas2 = data?.Gas2.length !== 15
  const gas7 = data?.Gas7.length !== 15
  status.smoke2 = gas2
  status.smoke7 = gas7
}

timer = setInterval(getDataFromJSON, 5000)
onMounted(() => {
  getDataFromJSON()
})
onUnmounted(() => {
  // Clear memory
  clearInterval(timer)
  timer = null
})
</script>

<template>
  <div class="index-panel border-1 h-[calc(100vh-100px)]">
    <p>{{ t('Date') }}: {{ lastUpdateTime }}</p>
    <div class="index-panel-core m-x-auto flex  sm:w-100px md:w-700px lg:w-700px xl:w-700px 2xl:w-700px m-y-200px text-center color-auto  items-center justify-center bg-transparent">
      <!-- Humidity -->
      <div class="w-100px h-100px inline-block bg-yellow-100  dark-bg-yellow m-10px rounded flex flex-col justify-center">
        <h2>
          {{ t('Humid') }}
        </h2>
        <div class="flex items-center  justify-items-center translate-x-5px">
          <div class="i-mdi-water-percent text-2xl inline-block" /><span>{{ `${status.humidity}%` }}</span>
        </div>
      </div>
      <!-- Temperature -->
      <div class="inline-block w-100px h-100px bg-blue-100 dark-bg-paleblue m-10px rounded flex flex-col justify-center">
        <h2>
          {{ t('Temperature') }}
        </h2>
        <div class="inline-block flex items-center justify-items-center translate-x-5px">
          <div class="i-mdi-thermometer text-2xl inline-block" /><span>{{ status.temperature }}℃</span>
        </div>
      </div>
      <!-- Rain -->
      <div class="inline-block w-100px h-100px bg-purple-100 dark-bg-purple-500  m-10px rounded flex flex-col justify-center" :class="{ '!bg-red-500': status.rain, 'dark-bg-red-500': status.rain }">
        <h2>
          {{ t('rain') }}
        </h2>
        <div class="flex items-center justify-items-center translate-x-5px">
          <div class="i-mdi-weather-pouring text-2xl inline-block" /><span>{{ status.rain ? 'ALERT' : 'NORMAL' }}</span>
        </div>
      </div>
      <!-- Smoke 2 -->
      <div class="inline-block w-100px h-100px bg-gray-100 dark-bg-gray-500  m-10px rounded flex flex-col justify-center" :class="{ '!bg-red-500': status.smoke2, 'dark-bg-red-500': status.smoke2 }">
        <h2>
          {{ t('Gas') }} #2
        </h2>
        <div class="flex items-center justify-items-center translate-x-5px">
          <div class="i-mdi-smoke text-2xl inline-block" /><span>{{ status.smoke2 ? 'ALERT' : 'NORMAL' }}</span>
        </div>
      </div>
      <!-- Smoke 7 -->
      <div class="inline-block w-100px h-100px bg-gray-100 dark-bg-gray-500  m-10px rounded flex flex-col justify-center" :class="{ '!bg-red-500': status.smoke7, 'dark-bg-red-500': status.smoke7 }">
        <h2>
          {{ t('Gas') }} #7
        </h2>
        <div class="flex items-center justify-items-center translate-x-5px">
          <div class="i-mdi-smoke text-2xl inline-block" /><span>{{ status.smoke7 ? 'ALERT' : 'NORMAL' }}</span>
        </div>
      </div>
      <!-- Dust -->
      <div
        class="w-100px h-100px  bg-gray-300 dark-bg-black  rounded flex flex-col justify-center"
        :class="{ '!bg-red-500': status.dust, 'dark-bg-red-500': status.dust }"
      >
        <h2>
          {{ t('Dust') }}
        </h2>
        <div class="flex items-center justify-items-center translate-x-5px">
          <div class="i-mdi-weather-dust text-2xl inline-block" /><span>OFFLINE</span>
          <!-- <div class="i-mdi-weather-dust text-2xl inline-block" /><span>{{ status.dust ? 'ALERT' : 'NORMAL' }}</span> -->
        </div>
      </div>
    </div>

    <a rel="noreferrer" href="https://github.com/FlynnCao/WOC7020-Smart-Home" target="_blank">
      {{ t('author') }}
    </a>
  </div>
</template>

<route lang="yaml">
meta:
  layout: home
</route>
