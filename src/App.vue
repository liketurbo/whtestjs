<script setup>
import { ref } from 'vue'

import Calendar from './components/Calendar.vue'

let date = ref(new Date().toISOString().split('T')[0]);
</script>

<template>
  <h1 class="green">{{ date }}</h1>
  <Calendar @date-select="(d) => date = d" />
</template>