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
        <template v-for="(step, index) in data" :key="index">
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
              {{ isCurrentStep(index) && score ? score + " / " : "" }}
              {{ step }}
            </span>
          </div>
        </template>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { computed, toRefs} from 'vue'
import IconsController from '@/components/IconsController.vue'

const props = defineProps({
  data: {
    type: Object,
    required: true
  },
  score: {
    type: Number,
    required: false,
    default: 0
  }
})

const { data, score } = toRefs(props)
const stepsLength = computed(() => data.value.length)
const stepPercent = computed(() => 100 / stepsLength.value)

const calcPercent = () => {
  let total = 0
  let preStep = 0
  data.value.forEach((point: number, index: number) => {
    if (score.value >= preStep && score.value <= point) {
      const stepNumber = score.value - preStep
      const stepAmount = point - preStep
      const percentageOfStepNumber = (stepNumber * 100) / stepAmount
      const percentageOfTheScaleNumber = (percentageOfStepNumber * 100) / (stepsLength.value * 100)
      total = percentageOfTheScaleNumber + index * stepPercent.value
    }
    preStep = point
  })
  return total
}

const progress = computed(calcPercent)

const isCurrentStep = (index: number) => {
  if (score.value < data.value[index - 1]) {
    return false
  }
  return (
      score.value < data.value[index] &&
      score.value < data.value[index + 1]
  )
}

const isComplete = (index: number) => score.value >= data.value[index] ?? false
</script>

<style lang='scss' scoped>
@import "@/assets/variables.scss";

.progress-bar__scale {
  position: relative;
  width: 900px;
  height: 40px;
  background: $progress-bar-bg;
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
  border-right: 2px solid $steps-color;
}
.progress-bar__step:last-child {
  border-right: none;
}
.progress-bar__indicator {
  height: 100%;
  background: $primary;
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
  fill: $primary;
}
.progress-bar__step:last-child .progress-bar__top {
  width: auto;
  top: -30px;
  right: 0px;
}

.progress-bar__step:last-child .progress-bar__bottom {
  right: 0px;
}
</style>
