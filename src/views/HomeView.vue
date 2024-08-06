<template>
    <div class="container mx-auto p-6">
        <h1 class="text-2xl text-gray-500 font-medium mb-4 tracking-tight">
            Probably the best JSON Viewer on our planet.
        </h1>
        <textarea
            v-model="jsonInput"
            class="w-full h-64 p-2 border border-gray-300 rounded mb-4"
            placeholder="Paste your JSON here"
        ></textarea>
        <button
            @click="parseJson"
            class="bg-blue-500 text-white py-4 px-6 rounded-2xl mb-4 hover:bg-blue-600 active:bg-blue-400 ring-2 ring-slate-300"
        >
            Parse JSON
        </button>
        <button
            @click="clearInput"
            class="bg-red-500 text-white py-4 px-6 ml-4 rounded-2xl mb-4 hover:bg-red-600 active:bg-red-400 ring-2 ring-slate-100"
        >
            Clear
        </button>
        <div v-if="error" class="text-red-500 mb-4 mt-4">{{ error }}</div>
        <JsonViewer v-if="jsonData" :jsonData="jsonData" />
    </div>
</template>

<script setup lang="ts">
import { ref, onMounted } from "vue";
import JsonViewer from "@/components/JsonViewer.vue";

const jsonInput = ref<string>("");
const jsonData = ref<any>(null);
const error = ref<string | null>(null);

const preprocessJsonInput = (input: string) => {
    let processedInput = input.trim();

    // Check if the input is stringified JSON
    if (processedInput.startsWith('"') && processedInput.endsWith('"')) {
        processedInput = JSON.parse(processedInput);
    }

    // Replace single quotes with double quotes for values
    processedInput = processedInput.replace(/'([^']*)'/g, '"$1"');

    // Handle single quotes inside string values by escaping them
    processedInput = processedInput.replace(
        /:(\s*)("(?:[^"\\]|\\.)*?")(\s*)(?=[:,}])/g,
        (_, p1, p2, p3) => {
            return `:${p1}${p2.replace(/'/g, "\\'")}${p3}`;
        }
    );

    // Add double quotes around unquoted keys
    processedInput = processedInput.replace(/([{,]\s*)([a-zA-Z0-9_]+)(\s*:)/g, '$1"$2"$3');

    return processedInput;
};

const parseJson = () => {
    try {
        const processedInput = preprocessJsonInput(jsonInput.value);
        jsonData.value = JSON.parse(processedInput);
        error.value = null;
        localStorage.setItem("jsonInput", jsonInput.value);
    } catch (e: any) {
        highlightErrorPosition(e);
        error.value = e?.message || "Invalid JSON. Please check the input and try again.";
        jsonData.value = null;
    }
};

// Highlight error position in the console
const highlightErrorPosition = (error: Error) => {
    const positionMatch = error.message.match(/position (\d+)/);
    if (positionMatch) {
        const position = parseInt(positionMatch[1], 10);
        const processedInput = preprocessJsonInput(jsonInput.value);
        const beforeError = processedInput.substring(0, position);
        const afterError = processedInput.substring(position);

        console.log(
            `%c${beforeError}%c${afterError}`,
            "color: black;",
            "color: red; font-weight: bold;"
        );
    }
};

// Clear input and JSON data
const clearInput = () => {
    jsonInput.value = "";
    jsonData.value = null;
    error.value = null;
    localStorage.removeItem("jsonInput");
};

// Retrieve input from localStorage on mounted
onMounted(() => {
    const savedInput = localStorage.getItem("jsonInput");
    if (savedInput) {
        jsonInput.value = savedInput;
        parseJson();
    }
});
</script>

<style>
/* You can add global styles here */
</style>
