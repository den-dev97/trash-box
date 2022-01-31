<template>
  <section class="section">
    <h1 v-if="!isShowResult" class="header">Выберите правильный бак <br> для сортировки мусора!</h1>
    <h1 v-else class="header">Отлично! Ты набрал {{ success }} очков <br> из {{ success + failure }}.</h1>

    <div class="main">
      <div v-if="isShowInfo" class="success"><span>✔</span>{{ success }}</div>
      <Timer @timerStart="onTimerStart" @timerEnd="onTimerEnd">
        <transition :name="movingAnimation">
          <img v-show="movingImage" class="trash" :src="`src/assets/trash/${trash[0].img}.png`" :title="trash[0].name"
               :alt="trash[0].name">
        </transition>
      </Timer>
      <div v-if="isShowInfo" class="failure"><span>✘</span>{{ failure }}</div>
    </div>

    <div class="tanks">
      <button class="tank-button" v-for="tank in tanks" @click="sortingTrash(tank.id)" :disabled="!isTimerWork">
        <transition name="cap-animation">
          <img v-show="capAnimation" class="cap" :src="`src/assets/buck_top/${tank.cap}.png`" alt="cap">
        </transition>
        <span class="label" :style="`background:${tank.color}`">{{ tank.name }}</span>
        <img class="tank" :src="`src/assets/bucks/${tank.img}.png`" alt="tank">
      </button>
    </div>
  </section>
</template>

<script setup>
import Timer from "./components/Timer.vue";
import {ref, watch} from "vue";

const isTimerWork = ref(false)
const isShowInfo = ref(false)
const isShowResult = ref(false)
const movingImage = ref(true)
const capAnimation = ref(true)

const trash = ref([
  {name: 'огрызок', id: 1, img: 'apple'},
  {name: 'памперс', id: 2, img: 'pampers'},
  {name: 'бумага', id: 3, img: 'paper'},
  {name: 'батарейка', id: 4, img: 'battery'},
])
const tanks = [
  {name: 'Вторсырьё', id: 1, img: 'wastebox_yellow', color: 'orange', cap: 'wastetop_yellow'},
  {name: 'Смешанные', id: 2, img: 'wastebox_green', color: 'green', cap: 'wastetop_green'},
  {name: 'Бытовые', id: 3, img: 'wastebox_blue', color: 'blue', cap: 'wastetop_blue'},
  {name: 'Опасные', id: 4, img: 'wastebox_red', color: 'red', cap: 'wastetop_red'},
]
const success = ref(0)
const failure = ref(0)

const movingAnimation = ref('')

function sortingTrash(id) {
  capAnimation.value = false
  movingAnimation.value = 'animation-' + id
  movingImage.value = false
  setTimeout(() => {
    if (trash.value[0].id === id) {
      const firstItem = trash.value.shift()
      trash.value.push(firstItem)
      success.value++
    } else {
      const firstItem = trash.value.shift()
      trash.value.push(firstItem)
      failure.value++
    }
  }, 200)
}

watch(movingImage, () => {
  setTimeout(() => {
    movingImage.value = true
  }, 800)
  setTimeout(() => {
    capAnimation.value = true
  }, 500)
})

function onTimerStart() {
  failure.value = 0
  success.value = 0
  isTimerWork.value = true
  isShowInfo.value = true
  isShowResult.value = false
}

function onTimerEnd() {
  isTimerWork.value = false
  isShowResult.value = true
}

</script>

<style lang="sass">
#app
  font-family: Avenir, Helvetica, Arial, sans-serif
  -webkit-font-smoothing: antialiased
  -moz-osx-font-smoothing: grayscale
  display: flex
  justify-content: center
  align-items: center
  color: #2c3e50
  width: 100%
  height: 95vh

button
  padding: 0
  border: none
  color: inherit
  background-color: transparent
  cursor: pointer
  font-weight: 700

.section
  width: 700px
  height: 450px
  background-color: antiquewhite
  border-radius: 2rem
  padding: 3rem
  overflow: hidden

.header
  text-align: center
  margin-top: 0

.tanks
  display: flex
  justify-content: center
  position: relative
  top: 50px
  gap: 1rem

.tank-button
  position: relative

.tank
  margin-top: 2rem
  width: 140px
  height: 200px
  z-index: 2
  position: relative

.label
  padding: .8rem 1rem
  color: white
  border-radius: 2rem
  font-size: 1rem

.main
  height: 200px
  display: flex
  color: white
  align-items: center
  justify-content: space-evenly

.success
  background: green

.failure
  background: red

.success, .failure
  height: min-content
  padding: 1rem 2rem
  border-radius: 3rem
  font-size: 2rem
  font-weight: bold

  span
    margin-right: .7rem

.trash
  width: 100px
  height: 70px
  position: absolute
  left: 26%
  top: 30%

.animation-1-leave-active
  transition: all .3s linear

.animation-1-leave-active
  width: 50px
  height: 35px
  left: -13rem
  top: 19rem

.animation-2-leave-active
  transition: all .3s linear

.animation-2-leave-active
  width: 50px
  height: 35px
  left: -13%
  top: 155%

.animation-3-leave-active
  transition: all .3s linear

.animation-3-leave-active
  width: 50px
  height: 35px
  left: 76%
  top: 155%

.animation-4-leave-active
  transition: all .3s linear

.animation-4-leave-active
  width: 50px
  height: 35px
  left: 161%
  top: 151%

.cap
  width: 110px
  height: 25px
  top: 45px
  left: 25px
  position: absolute

.cap-animation-enter-active
  top: 45px
  transition: all .4s

.cap-animation-enter, .cap-animation-leave-to
  transition: all .4s
  top: 30px

@media (max-width: 630px)
  .tanks
    gap: 0
  .tank
    width: 100%
    margin-top: 6rem
  .cap
    display: none
  .label
    transform: rotate(300deg)
    position: absolute
</style>
