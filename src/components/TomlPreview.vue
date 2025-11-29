<template>
  <div class="bg-white rounded-2xl shadow-lg p-6 h-full flex flex-col sticky top-8">
    <div class="flex items-center gap-3 mb-5">
      <svg class="w-6 h-6 text-emerald-600" fill="none" stroke="currentColor" viewBox="0 0 24 24">
        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 12h6m-6 4h6m2 5H7a2 2 0 01-2-2V5a2 2 0 012-2h5.586a1 1 0 01.707.293l5.414 5.414a1 1 0 01.293.707V19a2 2 0 01-2 2z" />
      </svg>
      <h2 class="text-xl font-semibold text-slate-800">TOML 预览</h2>
    </div>

    <div class="text-sm text-slate-600 mb-4">实时查看生成的TOML代码</div>

    <div class="flex-1 bg-slate-900 rounded-xl p-6 overflow-auto shadow-inner">
      <div class="toml-highlight" v-html="highlightedToml"></div>
    </div>

    <div class="mt-4 flex items-center gap-2">
      <button
        @click="copyToClipboard"
        class="flex-1 px-4 py-2.5 bg-slate-600 hover:bg-slate-700 text-white rounded-lg font-medium transition flex items-center justify-center gap-2"
      >
        <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M8 16H6a2 2 0 01-2-2V6a2 2 0 012-2h8a2 2 0 012 2v2m-6 12h8a2 2 0 002-2v-8a2 2 0 00-2-2h-8a2 2 0 00-2 2v8a2 2 0 002 2z" />
        </svg>
        {{ copyButtonText }}
      </button>
    </div>
  </div>
</template>

<script setup>
import { computed, ref } from 'vue'

const props = defineProps({
  config: Object
})

const copyButtonText = ref('隐藏预览')

const tomlContent = computed(() => {
  return generateToml(props.config)
})

const highlightedToml = computed(() => {
  return highlightToml(tomlContent.value)
})

function generateToml(config) {
  let toml = ''

  if (config.model) toml += `model = ${config.model}\n`
  if (config.model_provider) toml += `model_provider = ${config.model_provider}\n`
  if (config.approval_policy) toml += `approval_policy = ${config.approval_policy}\n`
  if (config.sandbox_mode) toml += `sandbox_mode = ${config.sandbox_mode}\n`
  if (config.profile) toml += `profile = ${config.profile}\n`
  if (config.hide_agent_reasoning) toml += `hide_agent_reasoning = ${config.hide_agent_reasoning}\n`
  if (config.show_raw_agent_reasoning) toml += `show_raw_agent_reasoning = ${config.show_raw_agent_reasoning}\n`

  toml += '\n[model_providers]\n\n'
  toml += '[model_providers.openai]\n'
  toml += 'name = "OpenAI"\n'
  toml += 'base_url = "https://api.openai.com/v1"\n'
  toml += 'env_key = "OPENAI_API_KEY"\n'
  toml += 'wire_api = "responses"\n'
  toml += 'request_max_retries = 4\n'
  toml += 'stream_max_retries = 5\n'
  toml += 'stream_idle_timeout_ms = 300000\n\n'

  toml += '[model_providers.ollama]\n'
  toml += 'name = "Ollama"\n'
  toml += 'base_url = "http://localhost:11434/v1"\n\n'

  toml += '[model_providers.azure]\n'
  toml += 'name = "Azure"\n'
  toml += 'base_url = "https://YOUR_AZURE_RESOURCE.openai.azure.com/openai/deployments/YOUR_DEPLOYMENT_NAME"\n'

  return toml
}

function highlightToml(toml) {
  let html = toml

  // Highlight sections
  html = html.replace(/\[([^\]]+)\]/g, '<span class="tok-section">[$1]</span>')

  // Highlight keys
  html = html.replace(/^(\s*)([a-zA-Z_][a-zA-Z0-9_]*)\s*=/gm, '$1<span class="tok-key">$2</span> =')

  // Highlight strings
  html = html.replace(/"([^"]*)"/g, '<span class="tok-string">"$1"</span>')

  // Highlight numbers
  html = html.replace(/\b(\d+)\b/g, '<span class="tok-number">$1</span>')

  // Highlight booleans
  html = html.replace(/\b(true|false)\b/g, '<span class="tok-boolean">$1</span>')

  return html
}

async function copyToClipboard() {
  try {
    await navigator.clipboard.writeText(tomlContent.value)
    copyButtonText.value = '已复制！'
    setTimeout(() => {
      copyButtonText.value = '隐藏预览'
    }, 2000)
  } catch (err) {
    console.error('Failed to copy:', err)
  }
}
</script>
