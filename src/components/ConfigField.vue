<template>
  <div class="mb-4 bg-slate-50 rounded-xl p-4 border border-slate-200">
    <div class="flex items-start gap-3">
      <div class="mt-1 flex-shrink-0">
        <svg class="w-4 h-4 text-blue-600" fill="currentColor" viewBox="0 0 20 20">
          <circle cx="10" cy="10" r="3"/>
        </svg>
      </div>
      <div class="flex-1 min-w-0">
        <div class="flex items-baseline gap-2 mb-1">
          <span class="font-medium text-slate-800 font-mono text-sm">{{ label }}</span>
          <span class="text-xs px-2 py-0.5 rounded-full"
                :class="typeClass">{{ type }}</span>
        </div>

        <input
          v-if="type !== 'Boolean'"
          :value="value"
          @input="$emit('update:value', $event.target.value)"
          :placeholder="placeholder"
          class="w-full px-3 py-2 mt-2 text-sm border border-slate-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-400 focus:border-blue-400 font-mono"
        />

        <select
          v-else
          :value="value"
          @change="$emit('update:value', $event.target.value)"
          class="w-full px-3 py-2 mt-2 text-sm border border-slate-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-400 focus:border-blue-400 font-mono"
        >
          <option value="true">true</option>
          <option value="false">false</option>
        </select>

        <div v-if="description" class="text-xs text-slate-500 mt-1.5">{{ description }}</div>

        <div v-if="$slots.options" class="mt-2 space-y-1">
          <slot name="options"></slot>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { computed } from 'vue'

const props = defineProps({
  label: String,
  type: String,
  value: [String, Boolean, Number],
  description: String,
  placeholder: String
})

defineEmits(['update:value'])

const typeClass = computed(() => {
  switch (props.type) {
    case 'String':
      return 'bg-emerald-100 text-emerald-700'
    case 'Boolean':
      return 'bg-orange-100 text-orange-700'
    case 'Number':
      return 'bg-blue-100 text-blue-700'
    default:
      return 'bg-gray-100 text-gray-700'
  }
})
</script>
