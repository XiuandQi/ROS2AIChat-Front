<template>
    <!-- 连接设置面板 -->
    <div class="settings-panel liquid-glass apple-radius" :class="{ 'hidden': modelValue }">
      <div class="settings-content">
        <div class="settings-header">
          <label for="ws-url">WebSocket地址</label>
          <button @click="$emit('update:modelValue', true)" class="close-btn" title="关闭">
            <i class="fas fa-times"></i>
          </button>
        </div>
        <div class="input-group">
          <input
            id="ws-url" 
            :value="wsUrl" 
            @input="$emit('update-ws-url', $event.target.value)"
            placeholder="ws://localhost:9090" class="apple_radius"
          />
          <button @click="handleConnect" class="connect-btn apple_radius">
            <i class="fas fa-plug"></i>
            <span>连接</span>
          </button>
        </div>
      </div>
    </div>
</template>

<script setup>
const props = defineProps({
  modelValue: {  // 改为使用v-model
    type: Boolean,
    required: true
  },
  wsUrl: {
    type: String,
    required: true
  }
})

const emit = defineEmits(['update:modelValue', 'update-ws-url', 'connect'])

function handleConnect() {
  emit('connect')
  // 连接成功后可以选择自动关闭面板
  // emit('update:modelValue', false)
}
</script>