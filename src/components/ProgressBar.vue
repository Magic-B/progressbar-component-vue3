<template>
  <div class="progress-bar" style="margin-top: 50px">
    <div class="progress-bar__scale">
      <div class="progress-bar__scale-container">
        <div
          class="progress-bar__indicator"
          :style="{ width: progress + '%' }"
        ></div>
      </div>
      <div class="progress-bar__steps">
        <template v-for="(step, index) in data.stages" :key="step.id">
          <div class="progress-bar__step">
            <IconsController
              class="progress-bar__top"
              :class="{
                'progress-bar__top--complete': isComplete(index),
                'progress-bar__top--active': isCurrentStep(index),
              }"
              :name="index === stepsLength - 1 ? 'full-path' : 'star'"
            />
            <span class="progress-bar__bottom">
              {{ isCurrentStep(index) ? result + " / " : "" }}
              {{ step.thresholdPoints }}
            </span>
          </div>
        </template>
      </div>
    </div>
  </div>
  <div style="margin: 50px auto">
    <input type="number" v-model="result" />
    <input type="number" v-model="progress" />
  </div>
</template>

<script setup lang="ts">
import { computed, ref } from "vue"
import { useDataStore } from "@/stores/data"
import IconsController from "@/components/IconsController.vue"
import { storeToRefs } from "pinia"

const dataStore = useDataStore()
const { data } = storeToRefs(dataStore)

const stepsLength = data.value.stages.length

const calculateResult = () => {
  return data.value.stages.reduce((accum, stage) => {
    return (
      accum +
      stage.games.reduce((accum, game) => {
        return accum + game.bestResult
      }, 0)
    )
  }, 0)
}

const result = ref(0)
result.value = calculateResult()

const calcPercent = () => {
  const stepsLength = data.value.stages.length
  const stepPercent = 100 / stepsLength

  let total = 0
  let last = 0
  data.value.stages.forEach((stage, index) => {
    if (result.value >= last && result.value <= stage.thresholdPoints) {
      const stepNumber = result.value - last
      const stepAmount = stage.thresholdPoints - last
      const percentageOfStepNumber = (stepNumber * 100) / stepAmount
      const percentageOfTheScaleNumber =
        (percentageOfStepNumber * 100) / (stepsLength * 100)
      total = percentageOfTheScaleNumber + index * stepPercent
    }
    last = stage.thresholdPoints
  })
  return total
}

const progress = computed(calcPercent)

const isCurrentStep = (index: number) => {
  if (result.value < data.value.stages[index - 1]?.thresholdPoints) {
    return false
  }
  return (
    result.value < data.value.stages[index].thresholdPoints &&
    result.value < data.value.stages[index + 1]?.thresholdPoints
  )
}

const isComplete = (index: any) => {
  return result.value >= data.value.stages[index]?.thresholdPoints ?? false
}
</script>

<style scoped>
.progress-bar__scale {
  position: relative;
  width: 900px;
  height: 40px;
  background: #efefef;
  border-radius: 30px;
}
.progress-bar__scale-container {
  position: absolute;
  width: 100%;
  height: 100%;
  border-radius: 30px;

  overflow: hidden;
}
.progress-bar__steps {
  position: relative;
  display: flex;
  height: 100%;
  width: 100%;
}
.progress-bar__step {
  position: relative;
  flex-grow: 1;
  z-index: 3;
  height: 100%;
  border-right: 2px solid #c4c4c4;
}
.progress-bar__step:last-child {
  border-right: none;
}
.progress-bar__indicator {
  height: 100%;
  background: #3300ff;
  z-index: 2;

  transition: width 300ms ease;
}
.progress-bar__top {
  position: absolute;
  width: 18px;
  top: -24px;
  right: -10px;
  z-index: 4;

  transition: all 400ms ease;
}
.progress-bar__bottom {
  position: absolute;
  text-align: center;
  width: 70px;
  bottom: -24px;
  right: -36px;
  z-index: 4;
}
.progress-bar__top--active {
  width: 22px;
  top: -30px;
  right: -13px;
}
.progress-bar__top--complete {
  width: 22px;
  top: -30px;
  right: -13px;
  fill: #3300ff;
}
.progress-bar__step:last-child .progress-bar__top {
  width: auto;
  right: 0px;
}

.progress-bar__step:last-child .progress-bar__bottom {
  right: 0px;
}
</style>
