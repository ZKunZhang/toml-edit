<template>
  <div class="text-xs text-slate-600 pl-3 py-1 hover:bg-slate-100 rounded cursor-pointer transition">
    <span class="font-mono text-blue-600">{{ value }}</span>
    <span v-if="description" class="text-slate-500"> — {{ description }}</span>
  </div>
</template>

<script setup>
defineProps({
  value: String,
  description: String
})
</script>
