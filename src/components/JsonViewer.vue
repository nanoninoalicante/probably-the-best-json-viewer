<template>
  <div>
    <div v-for="(value, key) in jsonData" :key="key" class="p-4 mt-4">
      <div
        @click="() => toggle(key)"
        class="cursor-pointer font-bold text-blue-500 hover:text-blue-800"
      >
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
import { ref, defineProps, watch } from 'vue'

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

// Initialize openKeys with all keys from jsonData
const initializeOpenKeys = (data: JsonData) => {
  for (const key in data) {
    if (data.hasOwnProperty(key)) {
      openKeys.value.push(key)
      if (isObject(data[key])) {
        initializeOpenKeys(data[key])
      }
    }
  }
}

// Watch for changes in jsonData and initialize openKeys
watch(
  () => props.jsonData,
  (newData) => {
    openKeys.value = []
    initializeOpenKeys(newData)
  },
  { immediate: true }
)
</script>

<style scoped>
/* You can add additional styles here */
</style>
