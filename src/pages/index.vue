<template>
  <main class="flex w-full h-screen justify-center items-center bg-gray-200">
    <div class="flex flex-col w-[95vw] max-w-[500px] bg-white rounded-lg p-6 gap-5">

      <div class="flex flex-row justify-between items-center">
        <img src="/turbine.png" alt="éolienne" class="w-8">
        <div class="flex flex-col items-end">
          <h3 class="font-bold text-lg">Wind Turbine #607</h3>
          <div class="flex flex-row items-center gap-1">
            <span :class="{'w-4 h-4 animate-pulse rounded-full': true, 'bg-red-400': status === 'Stopped', 'bg-orange-400': status === 'Starting' || status === 'Slowing', 'bg-green-400': status === 'Running'}"/>
            <h4 class="font-semibold text-gray-500">{{ status }}</h4>
          </div>
        </div>
      </div>

      <div class="grid grid-cols-2 grid-rows-3 gap-5">
        <div class="w-full h-full bg-gray-100 rounded-lg border-gray-200 border p-2">
          <h4 class="font-bold text-lg">{{ Math.round(OutputPower) }} kW</h4>
          <h5 class="text-gray-500">Power Output</h5>
        </div>
        <div class="w-full h-full bg-gray-100 rounded-lg border-gray-200 border p-2">
          <h4 class="font-bold text-lg">{{ WinSpeed }} m/s</h4>
          <h5 class="text-gray-500">Wind Speed</h5>
        </div>
        <div class="w-full h-full bg-gray-100 rounded-lg border-gray-200 border p-2">
          <h4 class="font-bold text-lg">{{ Efficiency }}%</h4>
          <h5 class="text-gray-500">Efficiency</h5>
        </div>
        <div class="w-full h-full bg-gray-100 rounded-lg border-gray-200 border p-2">
          <h4 class="font-bold text-lg">{{ YawDirection }}°</h4>
          <h5 class="text-gray-500">Yaw Direction</h5>
        </div>
        <div class="w-full h-full bg-gray-100 rounded-lg border-gray-200 border p-2">
          <h4 class="font-bold text-lg">{{ BladePitch }}°</h4>
          <h5 class="text-gray-500">Blade Pitch</h5>
        </div>
        <div class="w-full h-full bg-gray-100 rounded-lg border-gray-200 border p-2">
          <h4 class="font-bold text-lg">{{ Temperature }}°C</h4>
          <h5 class="text-gray-500">Temperature</h5>
        </div>
      </div>

      <button class="w-full bg-gray-800 text-white font-semibold p-3 rounded-xl" :disabled="status === 'Running' ? false: status !== 'Stopped'" @click="animateToZero" >{{ status === "Running" ? "Stop Turbine" : status === "Slowing" ? "Slowing..." : status === "Starting" ? "Starting..." : "Start Turbine" }}</button>

      <div :class="{'bg-gray-100 border border-gray-200 rounded-lg px-5 py-3': true, 'opacity-50 cursor-not-allowed': status !== 'Running'}">
        <h4 class="font-semibold">Yaw Direction</h4>
        <div class="flex flex-row justify-center items-center m-2">
          <h5>0°</h5>
          <input v-model="YawDirection" type="range" max="360" class="range-slider" :disabled="status !== 'Running'">
          <h5>360°</h5>
        </div>
        <h5 class="justify-self-center">{{ YawDirection }}°</h5>
      </div>

      <div :class="{'bg-gray-100 border border-gray-200 rounded-lg px-5 py-3': true, 'opacity-50 cursor-not-allowed': status !== 'Running'}">
        <h4 class="font-semibold">Blade Pitch</h4>
        <div class="flex flex-row justify-center items-center m-2">
          <h5>0°</h5>
          <input v-model="BladePitch" type="range" max="90" class="range-slider" :disabled="status !== 'Running'">
          <h5>90°</h5>
        </div>
        <h5 class="justify-self-center">{{ BladePitch }}°</h5>
      </div>

    </div>
  </main>
</template>

<script setup lang="ts">
// Stopped, Slowing, Starting, Running
const status = ref("Stopped")

const OutputPower = ref(0)
const WinSpeed = ref(0)
const Efficiency = ref(0)
const YawDirection = ref(0)
const BladePitch = ref(0)
const Temperature = ref(0)

const duration = 3000        // 3 secondes

let start = 0;
let end = 0;

let startTime:never;

function animate() {
  const now = performance.now()
  const elapsed = now - startTime
  if (elapsed < duration) {
    OutputPower.value = start - (elapsed / duration) * (start - end)
    requestAnimationFrame(animate)
  } else {
    OutputPower.value = end
    status.value = status.value === "Slowing" ? "Stopped" : "Running"
  }
}

function animateToZero() {
  if (status.value === "Running") {
    end = 0;
  } else {
    end = 350;
  }
  status.value = status.value === "Stopped" ? "Starting" : "Slowing"

  start = OutputPower.value;
  startTime = performance.now() as never;
  requestAnimationFrame(animate)
}

</script>

<style scoped>
.range-slider {
  @apply appearance-none bg-gray-200 rounded-lg h-2.5 w-full mx-3;
}
.range-slider:disabled {
  @apply cursor-not-allowed;
}
.range-slider::-webkit-slider-thumb {
  @apply appearance-none w-4 h-4 bg-gray-700 rounded-full cursor-pointer;
}
.range-slider::-webkit-slider-thumb:disabled {
  @apply cursor-not-allowed;
}
.range-slider::-moz-range-thumb {
  @apply appearance-none w-4 h-4 bg-gray-700 rounded-full cursor-pointer;
}
.range-slider::-moz-range-thumb:disabled {
  @apply cursor-not-allowed;
}
</style>