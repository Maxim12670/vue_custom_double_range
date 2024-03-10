<template>

  <div class="control">
    <input type="text" class="control__input" placeholder="min date" v-model="min">
    <input type="text" class="control__input" placeholder="max date" v-model="max">
  </div>

  <div class="slider">
    <input type="range" :min="min" :max="max" v-model="leftValue" ref="rangeLeft" class="slider__input"
      v-on:input="updateLine">
    <input type="range" :min="min" :max="max" v-model="rightValue" ref="rangeRight" class="slider__input"
      v-on:input="updateLine">
    <div class="values">
      <span>{{ leftValue }}</span> - <span>{{ rightValue }}</span>
    </div>
    <div class="slider__range-line" ref="line"></div>
    <div class="slider__ticks" id="sliderTicks"></div>
  </div>
</template>

<script setup>
import { computed, onMounted, ref, watch, watchEffect } from 'vue';
import './style.scss';

const min = ref(1);
const max = ref(10);
const leftValue = computed(() => min.value);
const rightValue = computed(() => max.value);
const rangeLeft = ref();
const rangeRight = ref();

const line = ref();

function updateLine() {
  const rangeWidth = rangeLeft.value.offsetWidth;
  const percent1 = (rangeLeft.value.value - min.value) / (max.value - min.value);
  const percent2 = (rangeRight.value.value - min.value) / (max.value - min.value);
  const leftPosition = percent1 * rangeWidth;
  const lineWidth = percent2 * rangeWidth - leftPosition;
  line.value.style.left = leftPosition + 'px';
  line.value.style.width = lineWidth + 'px';
}

function updateLineTicks() {
  line.value.innerHTML = '';

  const numTicks = max.value - 1;
  const range = parseInt(max.value) - parseInt(min.value);
  const step = range / numTicks;

  for (let i = 0; i <= numTicks; i++) {
    const tickValue = parseInt(min.value) + step * i;
    const tick = document.createElement('div');
    tick.className = 'tick';
    tick.style.left = `${(tickValue - parseInt(min.value)) / range * 100}%`;
    tick.textContent = tickValue;
    sliderTicks.appendChild(tick);
  }
}

onMounted(() => {
  console.log(111)
  updateLine();
  updateLineTicks();
})

watchEffect(min, () => {
  console.log(222)
  updateLine();
  updateLineTicks();
})

watchEffect(max, () => {

  updateLine();
  updateLineTicks();
})
</script>
