<template>
  <div class="control">
    <input v-model="min" type="text" class="control__input" placeholder="min date">
    <input v-model="max" type="text" class="control__input" placeholder="max date">
    <input v-model="currentMin" v-on:input="updateLine" type="text" class="control__input"
      placeholder="current min date">
    <input v-model="currentMax" v-on:input="updateLine" type="text" class="control__input"
      placeholder="current max date">
  </div>

  <div class="wrapper">
    <div class="switcher">
    <label class="switcher__label " @click="updateFlag" :class="{ 'switcher__label_active': switcherFlag }">
      Все года</label>
    <label class="switcher__label" @click="updateFlag"
      :class="{ 'switcher__label_active': !switcherFlag }">Месяца</label>
  </div>

  <div class="slider">
    <input :min="min" :max="max" v-model="leftValue" ref="rangeLeft" v-on:input="updateLine" type="range"
      class="slider__input">
    <input :min="min" :max="max" v-model="rightValue" ref="rangeRight" v-on:input="updateLine" type="range"
      class="slider__input">
    <div class="values">
      <span>{{ leftValue }}</span> - <span>{{ rightValue }}</span>
    </div>
    <div ref="line" class="slider__range-line"></div>
    <div ref="sliderTicks" class="slider__ticks"></div>
  </div>
  </div>
</template>

<script setup>
import { ref, watch } from 'vue';
import './style.scss';

const min = ref();
const max = ref();
const rangeLeft = ref();
const rangeRight = ref();
const currentMin = ref();
const currentMax = ref();
const leftValue = ref(currentMin);
const rightValue = ref(currentMax);
const line = ref(null);
const sliderTicks = ref();
const switcherFlag = ref(true);
const months = ['фев', 'мар', 'апр', 'май', 'июн', 'июл', 'авг', 'сен', 'окт', 'ноя', 'дек'];

function updateFlag() {
  switcherFlag.value = !switcherFlag.value;
}

function updateLine() {
  const rangeWidth = rangeLeft.value.offsetWidth;
  const leftValue = (rangeLeft.value.value - rangeLeft.value.min) / (rangeLeft.value.max - rangeLeft.value.min);
  const rightValue = (rangeRight.value.value - rangeRight.value.min) / (rangeRight.value.max - rangeRight.value.min);
  const leftPosition = leftValue * rangeWidth;
  const lineWidth = rightValue * rangeWidth - leftPosition;
  line.value.style.left = leftPosition + 'px';
  line.value.style.width = lineWidth + 'px';
}

function updateLineWithMonth() {
  const rangeWidth = rangeLeft.value.offsetWidth;
  const leftValue = (rangeLeft.value.value - rangeLeft.value.min) / (rangeLeft.value.max - rangeLeft.value.min);
  const rightValue = (rangeRight.value.value - rangeRight.value.min) / (rangeRight.value.max - rangeRight.value.min);
  const leftPosition = leftValue * rangeWidth;
  const lineWidth = rightValue * rangeWidth - leftPosition;
  line.value.style.left = leftPosition + 'px';
  line.value.style.width = lineWidth + 'px';
}

function tickWithYear() {
  sliderTicks.value.innerHTML = '';
  const numTicks = max.value - min.value;
  const range = parseInt(max.value) - parseInt(min.value);
  const step = range / numTicks;

  for (let i = 0; i <= numTicks; i++) {
    const tickValue = parseInt(min.value) + step * i;
    const tick = document.createElement('div');
    tick.className = 'tick';
    tick.style.left = `${(tickValue - parseInt(min.value)) / range * 100}%`;
    tick.textContent = tickValue;
    sliderTicks.value.appendChild(tick);
  }
}

function tickWithMonth() {
  sliderTicks.value.innerHTML = '';
  const numTicks = (max.value - min.value);
  const range = (parseInt(max.value) - parseInt(min.value));
  const step = range / numTicks;
  const widthForDate = parseInt(100 / numTicks);
  const widthForMonth = parseInt(widthForDate / 11);

  for (let i = 0; i <= numTicks; i++) {
    const tickValue = parseInt(min.value) + step * i;
    const tick = document.createElement('div');
    tick.className = 'tick';
    tick.style.left = `${widthForDate * i}%`;
    tick.style.width = `${widthForDate}%`;
    tick.textContent = tickValue;
    sliderTicks.value.appendChild(tick);

    for (let x = 0; x < 11; x++) {
      const tickMonth = document.createElement('div');
      tickMonth.className = 'tick__month';
      tickMonth.style.left = `${widthForMonth * x * i}%`;
      tickMonth.textContent = months[x];
      sliderTicks.value.appendChild(tickMonth);
    }
  }
}

function updateLineTicks() {
  if (max.value == 0) {
    sliderTicks.value.innerHTML = '';
  }
  if (switcherFlag.value) {
    tickWithYear();
  }
  else {
    tickWithMonth();
  }
}

watch(min, () => {
  updateLine();
  updateLineTicks();
})

watch(max, () => {
  updateLine();
  updateLineTicks();
})
</script>
