<script setup>
import { ref } from 'vue'

// TODO: Use DateTimeFormat from https://developer.mozilla.org/ru/docs/Web/JavaScript/Reference/Global_Objects/Date/getDay
const monthsEn = ["Jan", "Feb", "Mar", "Apr", "May", "Jun", "Jul", "Aug", "Sep", "Oct", "Nov", "Dec"];
const daysEn = ["Sun", "Mon", "Tue", "Wed", "Thu", "Fri", "Sat"];
const monthsRu = ["Янв", "Фев", "Мар", "Апр", "Май", "Июн", "Июл", "Авг", "Сен", "Окт", "Ноя", "Дек"];
const daysRu = ["Пн", "Вт", "Ср", "Чт", "Пт", "Cб", "Вск"];

const datePack = {
  en: {
    months: monthsEn,
    days: daysEn,
  },
  ru: {
    months: monthsRu,
    days: daysRu,
  }
}

const props = defineProps({
  // год-месяц-день"
  // 2024-03-18T11:49:44.303Z
  date: {
    type: String,
    required: false,
    default: new Date().toISOString().split('T')[0],
  },
  lang: {
    type: String,
    required: false,
    default: "en",
  }
});

const emit = defineEmits(["select-date"])

const { months, days } = datePack[props.lang];

let [y, m, d] = props.date.split('-');

const year = ref(parseInt(y));
const month = ref(parseInt(m) - 1);
const day = ref(parseInt(d));

function decMonth() {
  if (month.value === 0) {
    month.value = 11;
    year.value--;
  } else {
    month.value--;
  }

  // TODO: Handle day overflow
}

function incMonth() {
  if (month.value === 11) {
    month.value = 0;
    year.value++;
  } else {
    month.value++
  }

  // TODO: Handle day overflow
}


function lastMonthDayDate() {
  return new Date(year.value, month.value + 1, 0);
}

function firstMonthDayDate() {
  return new Date(year.value, month.value, 1);
}

function handleDayClick(n) {
  day.value = n;
  emit("date-select", `${year.value}-${(month.value + 1).toString().padStart(2, '0')}-${day.value.toString().padStart(2, '0')}`);
}
</script>

<template>
  <div class="container">
    <div class="header">
      <button @click="decMonth">⬅️</button>
      <select v-model="month">
        <option v-for="(m, i) in months" :selected="i === month" :value="i">
          {{ m }}
        </option>
      </select>
      <input class="header_year" type="number" v-model="year" />
      <button @click="incMonth">➡️</button>
    </div>
    <div class="days">
      <span class="days_day" v-for="d in days">{{ d }}</span>
      <button class="days_skip" v-for="_ in firstMonthDayDate().getDay()"></button>
      <button class="days_day days_clickable" :class="n === day && 'days_selected'"
        v-for="n in lastMonthDayDate().getDate()" @click="handleDayClick(n)">{{ n }}</button>
    </div>
  </div>
</template>

<style scoped>
.container {
  width: 225px;
}

.header {
  display: flex;
  justify-content: center;
}

.header_year {
  min-width: 60px;
}

.days {
  display: grid;
  grid-template-columns: repeat(7, 1fr);
}

.days_skip {
  visibility: hidden;
}

.days_day {
  text-align: center;
}

.days_clickable {
  cursor: pointer;
}

.days_selected {
  border: 2px solid #88d3e9;
}
</style>
