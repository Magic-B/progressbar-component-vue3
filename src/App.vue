<template>
  <div>
    <ProgressBar :data='points' :score='input'/>
    <div style="margin: 50px auto">
      <input type="number" v-model.number='input' :min='0' :max='points[points.length - 1]'/>
    </div>
  </div>
</template>

<script setup lang="ts">
import ProgressBar from '@/components/ProgressBar.vue'
import { useDataStore } from '@/stores/data'
import { storeToRefs } from 'pinia'
import { ref } from 'vue'
const dataStore = useDataStore()
const { data } = storeToRefs(dataStore)

const input = ref(0)

const calculateResult = (): number => {
  return data.value.stages.reduce((accum: number, stage) => {
    return (accum + stage.games.reduce((accum: number, game) => {
      return accum + game.bestResult
    }, 0))
  }, 0)
}

input.value = calculateResult()

const gettingPoints = (): number[] => {
  return data.value.stages.map(stage => stage.thresholdPoints)
}

const points = gettingPoints()
</script>
