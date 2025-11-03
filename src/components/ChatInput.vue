<template>
  <div class="chat-input liquid-glass apple-radius">
    <div class="input-container apple-radius">
      <textarea 
        ref="textareaRef"
        v-model="question"
        @keydown="handleKeydown"
        @input="adjustHeight"
        placeholder="请输入你的问题，例如：ROS是什么？" 
        rows="1"
      ></textarea>
      <button 
        @click="handleSpeechRecognition"
        class="speech-record-btn liquid-glass"
        :class="{ 'recording': isRecognizing }"
      >
        <i class="fas fa-microphone"></i>
      </button>
      <button 
        @click="handleSend"
        class="send-btn liquid-glass"
        :disabled="!connected || !question.trim()"
      >
        <i class="fas fa-paper-plane"></i>
      </button>
    </div>
  </div>
</template>

<script setup>
import { ref, nextTick } from 'vue'

defineProps({
  connected: {
    type: Boolean,
    required: true
  }
})

const emit = defineEmits(['send-message'])

const question = ref('')
const textareaRef = ref()
const isRecognizing = ref(false) // 是否正在识别

let recognition = null; // 语音识别实例
// 初始化 Web Speech API
window.SpeechRecognition = window.SpeechRecognition || window.webkitSpeechRecognition || null;

// 创建语音识别实例
recognition = new window.SpeechRecognition();
recognition.lang = 'zh-CN'; // 设置语言为中文
recognition.interimResults = false; // 启用中间结果
recognition.continuous = true; // 启用连续识别

recognition.onstart = () => {
    console.log('语音识别已启动。');
    isRecognizing.value = true;
};

// 语音识别出错时的回调
recognition.onerror = (event) => {
    console.error('语音识别错误:', event.error);
    alert(`语音识别错误: ${event.error}`);
    isRecognizing.value = false;
};

// 语音识别结束时的回调
recognition.onend = () => {
    console.log('语音识别已结束。');
    isRecognizing.value = false;
};

// 语音识别结果时的回调
recognition.onresult = (event) => {
  // 遍历所有识别结果
  for (let i = event.resultIndex; i < event.results.length; i++) {
      const result = event.results[i];
      const transcript = result[0].transcript.trim(); // 获取识别的文本
      console.log('识别结果:', transcript);
      question.value += transcript;
  }
};

function adjustHeight() {
  nextTick(() => {
    const textarea = textareaRef.value
    if (textarea) {
      textarea.style.height = 'auto'
      textarea.style.height = Math.min(textarea.scrollHeight, 120) + 'px'
    }
  })
}

function handleKeydown(event) {
  if (event.key === 'Enter' && !event.shiftKey) {
    event.preventDefault()
    handleSend()
  }
}

function handleSend() {
  if (!question.value.trim()) return
  
  const messageText = question.value.trim()
  emit('send-message', messageText)
  question.value = ''
  
  // 重置高度
  nextTick(() => {
    const textarea = textareaRef.value
    if (textarea) {
      textarea.style.height = 'auto'
    }
  })
}

function handleSpeechRecognition() {
  // 语音识别开始时的回调
  if (recognition && !isRecognizing.value) {
    recognition.start(); // 启动语音识别
    console.log('开始录音...');
  }else{
    recognition.stop(); // 停止语音识别
    console.log('停止录音...');
  }
}
</script>