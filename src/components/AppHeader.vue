<template>
  <div class="app-header liquid-glass">
    <!-- 头部信息（顶部居中） -->
     <div class="header-container">
        <div class="header-left">
        <div class="avatar liquid-glass" :class="connectionStatus.status">
            <i class="fas fa-robot"></i>
        </div>
        <div class="header-info">
            <h2>ROS2 AI助手</h2>
            <div class="connection-status">
            <span class="status-dot" :class="connectionStatus.status"></span>
            <span>{{ connectionStatus.text }}</span>
            </div>
        </div>
        </div>
        <!-- 头部操作按钮（顶部右侧） -->
        <div class="header-actions">
        <button class="action-btn trash apple-radius" @click="$emit('clear-chat')" title="清除对话">
            <i class="fas fa-trash"></i>
        </button>
        <button class="action-btn settings apple-radius" @click="$emit('toggle-settings')" title="连接设置">
            <i class="fas fa-cog"></i>
        </button>
        </div>
     </div>
  </div>
</template>

<script setup>
import { ref } from 'vue'
defineProps({
  connectionStatus: {
    type: Object,
    required: true
  }
})

defineEmits(['clear-chat', 'toggle-settings'])
</script>
