<template>
  <div>
    <button v-show="!isShowStartBtn && !timeLeft" class="more" @click="startTimer"><i/>Ещё раз</button>
    <div v-show="timeLeft" class="timer">
      <button v-show="isShowStartBtn" class="start" @click="startTimer">Начать</button>
      <svg class="svg" viewBox="0 0 100 100" xmlns="http://www.w3.org/2000/svg">
        <g class="circle">
          <circle class="elapsed" cx="50" cy="50" r="45"></circle>
          <path :stroke-dasharray="circleDasharray" class="remaining"
                d="M 50, 50
              m -45, 0
              a 45,45 0 1,0 90,0
              a 45,45 0 1,0 -90,0"
          />
        </g>
      </svg>
      <slot/>
    </div>
  </div>
</template>

<script setup>
import {computed, ref} from "vue";

const FULL_DASH_ARRAY = 283;
const TIME_LIMIT = 60;
const timePassed = ref(0)
const timerInterval = ref({})
const isShowStartBtn = ref(true)
const emit = defineEmits(['timerEnd', 'timerStart'])

const circleDasharray = computed(() => {
  return [(timeFraction.value * FULL_DASH_ARRAY), 283];
})
const timeLeft = computed(() => {
  if (timePassed.value === TIME_LIMIT) {
    return finishTimer()
  } else return TIME_LIMIT - timePassed.value
})
const timeFraction = computed(() => {
  const rawTimeFraction = timeLeft.value / TIME_LIMIT;
  return rawTimeFraction - (1 / TIME_LIMIT) * (1 - rawTimeFraction);
})

function startTimer() {
  emit('timerStart')
  timerInterval.value = setInterval(() => (timePassed.value += 1), 1000);
  isShowStartBtn.value = false
}

function finishTimer() {
  emit('timerEnd')
  clearInterval(timerInterval.value);
  timePassed.value = 0
  timerInterval.value = null
}
</script>

<style scoped lang="sass">
.timer
  position: relative
  width: 200px
  height: 200px
  margin: 0 auto

.circle
  fill: none
  stroke: none

.elapsed
  stroke-width: 7px

.remaining
  stroke-width: 7px
  stroke-linecap: round
  transform: rotate(90deg)
  transform-origin: center
  transition: 1s linear all
  fill-rule: nonzero
  stroke: currentColor
  color: mediumslateblue

.start
  width: 200px
  height: 200px
  background: mediumslateblue
  display: flex
  justify-content: center
  align-items: center
  border-radius: 100%
  position: absolute
  color: white
  font-size: 2rem
  z-index: 1

.more
  padding: 1rem 2rem
  text-align: center
  border-radius: 3rem
  color: white
  font-size: 2rem
  background-color: mediumslateblue
  display: flex
  align-items: center
  justify-content: center

  i
    background: url(./src/assets/reload.png) center no-repeat
    background-size: contain
    width: 30px
    height: 30px
    display: block
    margin-right: 1rem
</style>
