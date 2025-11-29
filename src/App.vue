<template>
  <div class="min-h-screen bg-gradient-to-r from-blue-50 to-cyan-50">
    <!-- Header -->
    <header class="bg-white/90 backdrop-blur-sm shadow-sm sticky top-0 z-50 border-b border-slate-200">
      <div class="max-w-7xl mx-auto px-6 py-4">
        <div class="flex items-center justify-between">
          <div class="flex items-center gap-3">
            <div class="w-10 h-10 bg-gradient-to-br from-blue-600 to-cyan-600 rounded-xl flex items-center justify-center shadow-lg">
              <svg class="w-6 h-6 text-white" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M10 20l4-16m4 4l4 4-4 4M6 16l-4-4 4-4" />
              </svg>
            </div>
            <div>
              <h1 class="text-2xl font-bold text-slate-900">TOML 编辑工具</h1>
              <p class="text-sm text-slate-500">拖拽、点击、编辑 - 让配置更更简单</p>
            </div>
          </div>

          <div class="flex items-center gap-3">
            <button
              @click="uploadFile"
              class="px-4 py-2.5 bg-blue-600 hover:bg-blue-700 text-white rounded-lg font-medium transition-all flex items-center gap-2 shadow-md hover:shadow-lg"
            >
              <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M7 16a4 4 0 01-.88-7.903A5 5 0 1115.9 6L16 6a5 5 0 011 9.9M15 13l-3-3m0 0l-3 3m3-3v12" />
              </svg>
              上传 TOML 文件
            </button>

            <button
              @click="downloadFile"
              class="px-4 py-2.5 bg-green-600 hover:bg-green-700 text-white rounded-lg font-medium transition-all flex items-center gap-2 shadow-md hover:shadow-lg"
            >
              <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4 16v1a3 3 0 003 3h10a3 3 0 003-3v-1m-4-4l-4 4m0 0l-4-4m4 4V4" />
              </svg>
              下载配置
            </button>

            <button
              @click="saveToLocal"
              class="px-4 py-2.5 bg-cyan-600 hover:bg-cyan-700 text-white rounded-lg font-medium transition-all flex items-center gap-2 shadow-md hover:shadow-lg"
            >
              <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M8 7H5a2 2 0 00-2 2v9a2 2 0 002 2h14a2 2 0 002-2V9a2 2 0 00-2-2h-3m-1 4l-3 3m0 0l-3-3m3 3V4" />
              </svg>
              保存版本
            </button>

            <button
              class="px-4 py-2.5 bg-slate-600 hover:bg-slate-700 text-white rounded-lg font-medium transition-all flex items-center gap-2 shadow-md hover:shadow-lg"
            >
              <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M15 12a3 3 0 11-6 0 3 3 0 016 0z" />
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M2.458 12C3.732 7.943 7.523 5 12 5c4.478 0 8.268 2.943 9.542 7-1.274 4.057-5.064 7-9.542 7-4.477 0-8.268-2.943-9.542-7z" />
              </svg>
              隐藏预览
            </button>
          </div>
        </div>
      </div>
    </header>

    <!-- Main Content -->
    <main class="max-w-7xl mx-auto px-6 py-8">
      <div class="grid xl:grid-cols-2 gap-8">
        <!-- Editor Panel -->
        <div class="h-[720px]">
          <TomlEditor @update:config="handleConfigUpdate" />
        </div>

        <!-- Preview Panel -->
        <div class="h-[720px]">
          <TomlPreview :config="currentConfig" />
        </div>
      </div>
    </main>

    <!-- Hidden file input -->
    <input
      ref="fileInput"
      type="file"
      accept=".toml"
      @change="handleFileUpload"
      class="hidden"
    />
  </div>
</template>

<script setup>
import { ref } from 'vue'
import TomlEditor from './components/TomlEditor.vue'
import TomlPreview from './components/TomlPreview.vue'

const currentConfig = ref({
  model: '"gpt-5"',
  model_provider: '"openai"',
  approval_policy: '"on-request"',
  sandbox_mode: '"workspace-write"',
  profile: '"daily"',
  hide_agent_reasoning: 'false',
  show_raw_agent_reasoning: 'false'
})

const fileInput = ref(null)

function handleConfigUpdate(newConfig) {
  currentConfig.value = { ...newConfig }
}

function uploadFile() {
  fileInput.value?.click()
}

function handleFileUpload(event) {
  const file = event.target.files?.[0]
  if (!file) return

  const reader = new FileReader()
  reader.onload = (e) => {
    try {
      const content = e.target?.result
      // Parse TOML content here
      console.log('TOML content:', content)
      // Update currentConfig with parsed values
    } catch (err) {
      console.error('Failed to parse TOML:', err)
    }
  }
  reader.readAsText(file)
}

function downloadFile() {
  const tomlContent = generateTomlFromConfig(currentConfig.value)
  const blob = new Blob([tomlContent], { type: 'text/plain' })
  const url = URL.createObjectURL(blob)
  const a = document.createElement('a')
  a.href = url
  a.download = 'config.toml'
  a.click()
  URL.revokeObjectURL(url)
}

function saveToLocal() {
  try {
    localStorage.setItem('toml-config', JSON.stringify(currentConfig.value))
    alert('配置已保存到本地！')
  } catch (err) {
    console.error('Failed to save:', err)
  }
}

function generateTomlFromConfig(config) {
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
</script>
