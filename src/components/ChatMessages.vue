<template>
  <div class="chat-messages">
    <div v-for="message,index in messages" :key="message.id">
        <div v-if="message.type === 'ai'" class="message-container" :class="index === messages.length - 1 ? 'leave-more-space' : ''">
            <div class="avatar liquid-glass" :class="{[messages[index].status]: true,'reading': messages[index].isReading}" @click="readAloud(messages[index])">
                <i class="fas fa-robot"></i>
            </div>
            <div class="message-bubble liquid-glass ai-message" :class="messages[index].status">
                <div class="message-content" v-html="message.content"></div>
            </div>
        </div>
        <div v-if="message.type === 'user'" class="message-bubble liquid-glass user-message">
          <div class="message-content" v-html="message.content"></div>
        </div>
    </div>
    <!-- 打字效果消息 -->
    <div v-if="currentTypingMessage && currentTypingMessage.isTyping">
        <div class="message-container ai-message leave-more-space">
            <div class="avatar liquid-glass working" :class="connectionStatus.status" >
                <i class="fas fa-robot"></i>
            </div>
            <div class="message-bubble liquid-glass ai-message typing-effect">
                <div class="message-content">
                    <span v-html="currentTypingMessage.content"></span>
                    <div v-if="currentTypingMessage.content==='AI正在思考'" class="typing-dots">
                        <span></span>
                        <span></span>
                        <span></span>
                    </div>
                    <span></span>
                </div>
            </div>
        </div>
    </div>
</div>
</template>

<script setup>
import { nextTick, watch } from 'vue'

const props = defineProps({
  messages: {
    type: Array,
    required: true
  },
  currentTypingMessage: {
    type: Object,
    default:{
      content: '',
      isTyping: false
    }
  },
  connectionStatus: {
    type: Object,
    required: true
  }
})

// 自动滚动到底部
function scrollToBottom() {
  nextTick(() => {
    const chatMessages = document.querySelector('.chat-messages')
    if (chatMessages) {
      chatMessages.scrollTop = chatMessages.scrollHeight
    }
  })
}

function readAloud(message) {
  if(!message.isReading){
    message.isReading = true
    const tempDiv = document.createElement('div');
    tempDiv.innerHTML = message.content
    const content = tempDiv.textContent
    const msg = new SpeechSynthesisUtterance(content)

    // 监听语音播放结束事件
    msg.onend = () => {
      speechRecover()
    }
    
    // 监听语音播放错误事件
    msg.onerror = () => {
      speechRecover()
    }

    // 设置语音属性
    msg.voice = speechSynthesis.getVoices()[196]
    speechSynthesis.speak(msg)
  }
}

function speechRecover() {
  props.messages.forEach(message => {
    message.isReading = false
  });
}

// 监听消息变化，自动滚动到底部
watch(() => props.messages, scrollToBottom, { deep: true })
watch(() => props.currentTypingMessage, scrollToBottom, { deep: true })
watch(() => props.currentTypingMessage?.content, scrollToBottom)
</script>