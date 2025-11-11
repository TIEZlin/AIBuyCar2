<!-- ChatInterface.vue -->
<template>
  <div class="chat-container">
    <!-- 聊天历史区域 -->
    <div class="chat-history" ref="chatHistory">
      <div 
        v-for="(message, index) in messages" 
        :key="index"
        :class="['message', message.sender]"
      >
        <div class="message-content">{{ message.content }}</div>
        <div class="timestamp">{{ message.timestamp }}</div>
      </div>
    </div>

    <!-- 输入区域 -->
    <div class="input-area">
      <textarea 
        v-model="userInput"
        placeholder="请输入您的购车需求..."
        @keyup.enter="sendMessage"
      ></textarea>
      <button @click="sendMessage">发送</button>
    </div>
  </div>
</template>

<script>
export default {
  name: 'ChatInterface',
  data() {
    return {
      messages: [
        {
          sender: 'system',
          content: '您好！欢迎使用购车咨询服务。请告诉我您的预算范围、车型偏好、使用场景等信息。',
          timestamp: this.getCurrentTime()
        }
      ],
      userInput: ''
    };
  },
  methods: {
    getCurrentTime() {
      const now = new Date();
      return `${now.getHours()}:${now.getMinutes().toString().padStart(2, '0')}`;
    },

    async sendMessage() {
      if (!this.userInput.trim()) return;

      // 添加用户消息到聊天记录
      this.messages.push({
        sender: 'user',
        content: this.userInput,
        timestamp: this.getCurrentTime()
      });

      // 保存用户输入以便后续处理
      const userQuery = this.userInput;
      this.userInput = '';

      // 模拟调用 LLM 服务获取推荐结果
      try {
        const response = await this.callLLMService(userQuery);
        
        // 添加系统回复到聊天记录
        this.messages.push({
          sender: 'system',
          content: response,
          timestamp: this.getCurrentTime()
        });
        
        // 滚动到底部
        this.$nextTick(() => {
          this.scrollToBottom();
        });
      } catch (error) {
        this.messages.push({
          sender: 'system',
          content: '抱歉，处理您的请求时出现错误，请稍后重试。',
          timestamp: this.getCurrentTime()
        });
      }
    },

    
    async callLLMService(query) {
      // 这里应该实际调用 LLMService.callAPI()
      // 暂时返回模拟数据
      return `根据您的需求，我们为您推荐以下车型：
1. 丰田卡罗拉 - 经济实用，适合城市通勤
2. 本田思域 - 动力强劲，外观时尚
3. 大众朗逸 - 空间宽敞，适合家庭使用`;
    },

    scrollToBottom() {
      const container = this.$refs.chatHistory;
      container.scrollTop = container.scrollHeight;
    }
  },

  mounted() {
    this.scrollToBottom();
  }
};

</script>

<style scoped>
.chat-container {
  display: flex;
  flex-direction: column;
  height: 100vh;
  max-width: 800px;
  margin: 0 auto;
  border: 1px solid #ddd;
}

.chat-history {
  flex: 1;
  overflow-y: auto;
  padding: 20px;
  background-color: #f9f9f9;
}

.message {
  margin-bottom: 15px;
  max-width: 80%;
}

.message.user {
  margin-left: auto;
}

.message.system {
  margin-right: auto;
}

.message-content {
  padding: 10px 15px;
  border-radius: 18px;
  word-wrap: break-word;
}

.message.user .message-content {
  background-color: #007bff;
  color: white;
  border-bottom-right-radius: 4px;
}

.message.system .message-content {
  background-color: #e9ecef;
  color: black;
  border-bottom-left-radius: 4px;
}

.timestamp {
  font-size: 12px;
  color: #6c757d;
  margin-top: 5px;
  text-align: right;
}

.input-area {
  display: flex;
  padding: 20px;
  border-top: 1px solid #ddd;
  background-color: white;
}

textarea {
  flex: 1;
  padding: 10px;
  border: 1px solid #ddd;
  border-radius: 4px;
  resize: none;
  height: 60px;
}

button {
  margin-left: 10px;
  padding: 10px 20px;
  background-color: #007bff;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
}
</style>