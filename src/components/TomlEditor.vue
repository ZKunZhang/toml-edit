<template>
  <div class="bg-white rounded-2xl shadow-lg p-6 h-full flex flex-col">
    <div class="flex items-center justify-between mb-5">
      <div class="flex items-center gap-3">
        <svg class="w-6 h-6 text-teal-600" fill="none" stroke="currentColor" viewBox="0 0 24 24">
          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M10.325 4.317c.426-1.756 2.924-1.756 3.35 0a1.724 1.724 0 002.573 1.066c1.543-.94 3.31.826 2.37 2.37a1.724 1.724 0 001.065 2.572c1.756.426 1.756 2.924 0 3.35a1.724 1.724 0 00-1.066 2.573c.94 1.543-.826 3.31-2.37 2.37a1.724 1.724 0 00-2.572 1.065c-.426 1.756-2.924 1.756-3.35 0a1.724 1.724 0 00-2.573-1.066c-1.543.94-3.31-.826-2.37-2.37a1.724 1.724 0 00-1.065-2.572c-1.756-.426-1.756-2.924 0-3.35a1.724 1.724 0 001.066-2.573c-.94-1.543.826-3.31 2.37-2.37.996.608 2.296.07 2.572-1.065z" />
          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M15 12a3 3 0 11-6 0 3 3 0 016 0z" />
        </svg>
        <h2 class="text-xl font-semibold text-slate-800">可视化编辑器</h2>
      </div>
      <div class="flex items-center gap-2">
        <span class="text-sm text-slate-500">版本:</span>
        <select v-model="version" class="px-3 py-1.5 text-sm border border-slate-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-400">
          <option value="v1">默认最佳实践</option>
        </select>
      </div>
    </div>

    <div class="text-sm text-slate-600 mb-4">直观编辑你的TOML配置</div>

    <div class="space-y-4 overflow-auto flex-1 pr-2">
      <div class="mb-6">
        <h3 class="text-sm font-semibold text-slate-700 mb-3 flex items-center gap-2">
          <span class="w-2 h-2 bg-blue-500 rounded-full"></span>
          配置项目
        </h3>

        <!-- Model Field -->
        <ConfigField
          label="model"
          type="String"
          :value="config.model"
          @update:value="config.model = $event"
          description="当前使用的推理模型"
          placeholder='"gpt-5"'
        >
          <template #options>
            <FieldOption value='"gpt-5"' description="当前使用的推理模型" />
          </template>
        </ConfigField>

        <!-- Model Provider Field -->
        <ConfigField
          label="model_provider"
          type="String"
          :value="config.model_provider"
          @update:value="config.model_provider = $event"
          description="默认模型提供商标识"
          placeholder='"openai"'
        >
          <template #options>
            <FieldOption value="openai" description="OpenAI 官方服务" />
            <FieldOption value="ollama" description="本地 Ollama 服务" />
            <FieldOption value="azure" description="Azure OpenAI 服务" />
            <FieldOption value="openai-chat-completions" description="Chat Completions 兼容接口" />
          </template>
        </ConfigField>

        <!-- Approval Policy Field -->
        <ConfigField
          label="approval_policy"
          type="String"
          :value="config.approval_policy"
          @update:value="config.approval_policy = $event"
          description="Shell 命令审批策略 (影响权限请求)"
          placeholder='"on-request"'
        >
          <template #options>
            <FieldOption value="untrusted" description="执行前总是提醒" />
            <FieldOption value="on-failure" description="失败时审核请求 (默认)" />
            <FieldOption value="on-request" description="按需开启权限 (默认)" />
            <FieldOption value="never" description="无需确认，自动执行" />
          </template>
        </ConfigField>

        <!-- Sandbox Mode Field -->
        <ConfigField
          label="sandbox_mode"
          type="String"
          :value="config.sandbox_mode"
          @update:value="config.sandbox_mode = $event"
          description="执行沙箱策略"
          placeholder='"workspace-write"'
        >
          <template #options>
            <FieldOption value="read-only" description="只读模式，禁止写入" />
            <FieldOption value="workspace-write" description="仅在工作区可写 (默认)" />
            <FieldOption value="danger-full-access" description="无限制访问，风险较高" />
          </template>
        </ConfigField>

        <!-- Profile Field -->
        <ConfigField
          label="profile"
          type="String"
          :value="config.profile"
          @update:value="config.profile = $event"
          description="默认启用的配置档案"
          placeholder='"daily"'
        >
          <template #options>
            <FieldOption value="daily" description="日常开发配置" />
            <FieldOption value="fast" description="快速响应模式" />
            <FieldOption value="deep" description="深度推理模式" />
            <FieldOption value="offline" description="离线/本地模式" />
          </template>
        </ConfigField>

        <!-- Hide Agent Reasoning -->
        <ConfigField
          label="hide_agent_reasoning"
          type="Boolean"
          :value="config.hide_agent_reasoning"
          @update:value="config.hide_agent_reasoning = $event"
          description="是否隐藏模型推理过程显示"
        >
          <template #options>
            <FieldOption value="true" description="隐藏推理过程" />
            <FieldOption value="false" description="显示推理过程（默认）" />
          </template>
        </ConfigField>

        <!-- Show Raw Agent Reasoning -->
        <ConfigField
          label="show_raw_agent_reasoning"
          type="Boolean"
          :value="config.show_raw_agent_reasoning"
          @update:value="config.show_raw_agent_reasoning = $event"
          description="是否展示原始推理过程"
        >
          <template #options>
            <FieldOption value="true" description="展示原始格式" />
            <FieldOption value="false" description="使用格式化展示（默认）" />
          </template>
        </ConfigField>

        <!-- Add Item Button -->
        <button
          @click="addField"
          class="w-full mt-4 px-4 py-2.5 bg-green-500 hover:bg-green-600 text-white rounded-lg font-medium transition flex items-center justify-center gap-2"
        >
          <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 4v16m8-8H4" />
          </svg>
          添加节
        </button>
      </div>
    </div>
  </div>
</template>

<script setup>
import { reactive, watch } from 'vue'
import ConfigField from './ConfigField.vue'
import FieldOption from './FieldOption.vue'

const emit = defineEmits(['update:config'])

const version = reactive({ value: 'v1' })

const config = reactive({
  model: '"gpt-5"',
  model_provider: '"openai"',
  approval_policy: '"on-request"',
  sandbox_mode: '"workspace-write"',
  profile: '"daily"',
  hide_agent_reasoning: 'false',
  show_raw_agent_reasoning: 'false'
})

watch(config, (newConfig) => {
  emit('update:config', newConfig)
}, { deep: true })

function addField() {
  // Placeholder for adding new field functionality
  console.log('Add new field')
}
</script>
