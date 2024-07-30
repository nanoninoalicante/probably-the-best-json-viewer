<template>
  <div>
    <div v-for="(value, key) in jsonData" :key="key" class="p-2">
      <div @click="() => toggle(key)" class="cursor-pointer font-bold text-blue-500">
        {{ key }}: <span v-if="isObject(value)">{...}</span>
      </div>
      <div v-if="openKeys.includes(key)" class="ml-4">
        <JsonViewer v-if="isObject(value)" :jsonData="value" />
        <div v-else>{{ value }}</div>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, defineProps } from 'vue'

interface JsonData {
  [key: string]: any
}

const props = defineProps<{ jsonData: JsonData }>()

const openKeys = ref<string[]>([])

const toggle = (key: string) => {
  const index = openKeys.value.indexOf(key)
  if (index === -1) {
    openKeys.value.push(key)
  } else {
    openKeys.value.splice(index, 1)
  }
}

const isObject = (value: any) => {
  return value && typeof value === 'object'
}
</script>

<style scoped>
/* You can add additional styles here */
</style>
