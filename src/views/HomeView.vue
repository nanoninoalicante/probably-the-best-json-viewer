<template>
  <div class="container mx-auto p-4">
    <h1 class="text-2xl font-bold mb-4">JSON Viewer</h1>
    <textarea
      v-model="jsonInput"
      class="w-full h-64 p-2 border border-gray-300 rounded mb-4"
      placeholder="Paste your JSON here"
    ></textarea>
    <button @click="parseJson" class="bg-blue-500 text-white py-2 px-4 rounded mb-4">
      Parse JSON
    </button>
    <div v-if="error" class="text-red-500 mb-4">{{ error }}</div>
    <JsonViewer v-if="jsonData" :jsonData="jsonData" />
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'
import JsonViewer from '@/components/JsonViewer.vue'

const jsonInput = ref<string>('')
const jsonData = ref<any>(null)
const error = ref<string | null>(null)

const preprocessJsonInput = (input: string) => {
  return input.replace(/([{,]\s*)([a-zA-Z0-9_]+)(\s*:)/g, '$1"$2"$3')
}

const parseJson = () => {
  try {
    const processedInput = preprocessJsonInput(jsonInput.value)
    jsonData.value = JSON.parse(processedInput)
    error.value = null
  } catch (e) {
    error.value = 'Invalid JSON. Please check the input and try again.'
    jsonData.value = null
  }
}
</script>

<style>
/* You can add global styles here */
</style>
