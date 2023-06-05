<template>
  <div class='progress-bar' style='margin-top: 50px'>
    <div class='progress-bar__scale'>
      <div class='progress-bar__scale-container'>
        <div class="progress-bar__indicator" :style="{ width: progress + '%' }"></div>
      </div>
      <div class='progress-bar__steps'>
        <template v-for='(step, index) in steps' :key='step'>
          <div class='progress-bar__step'>
            <IconsController class='progress-bar__top' :name='index + 1 === steps ? "full-path": "star"' />
            <span class='progress-bar__bottom'>20</span>
          </div>
        </template>
      </div>
    </div>
  </div>
  <div style='margin: 50px auto'>
    <input type="number" v-model='progress'>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'
import IconsController from '@/components/IconsController.vue'

const steps = ref(5)
const progress = ref(100)
</script>

<style scoped>
.progress-bar__scale {
  position: relative;
  width: 900px;
  height: 40px;
  background: #EFEFEF;
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
  border-right: 2px solid #e3a55c;
}
.progress-bar__step:last-child {
  border-right: none;
}
.progress-bar__indicator {
  height: 100%;
  background: purple;
  z-index: 2;

  transition: width 300ms ease;
}
.progress-bar__top {
  position: absolute;
  top: -24px;
  right: -10px;
  z-index: 4;
}
.progress-bar__bottom {
  position: absolute;
  bottom: -24px;
  right: -10px;
  z-index: 4;
}

.progress-bar__step:last-child .progress-bar__top {
  right: 0px;
}

.progress-bar__step:last-child .progress-bar__bottom {
  right: 0px;
}
</style>
