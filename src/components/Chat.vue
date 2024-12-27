<!-- eslint-disable vue/multi-word-component-names -->
<template>
  <div class="flex flex-col h-screen bg-gray-100">
    <header
      class="flex items-center justify-between px-6 py-4 bg-blue-500 text-white shadow"
    >
      <h1 class="text-lg font-bold">Chat with Bot</h1>
      <button
        @click="logout"
        class="bg-red-500 px-4 py-2 rounded hover:bg-red-600"
      >
        Logout
      </button>
    </header>
    <main class="flex-1 p-4 overflow-y-auto">
      <div v-for="(log, index) in chatLogs" :key="index" class="mb-4">
        <p class="text-sm text-gray-600">
          <strong>You:</strong> {{ log.message }}
        </p>
        <p class="text-sm text-gray-800">
          <strong>Bot:</strong> {{ log.response }}
        </p>
      </div>
    </main>
    <footer class="p-4 bg-white shadow">
      <form @submit.prevent="sendMessage" class="flex">
        <input
          v-model="message"
          type="text"
          placeholder="Type a message..."
          class="flex-1 px-4 py-2 border rounded focus:outline-none focus:ring"
          required
        />
        <button
          type="submit"
          class="ml-2 bg-blue-500 text-white px-4 py-2 rounded hover:bg-blue-600"
        >
          Send
        </button>
      </form>
    </footer>
  </div>
</template>

<script>
import axios from "axios";

export default {
  data() {
    return {
      message: "",
      chatLogs: [],
    };
  },
  methods: {
    async fetchChatHistory() {
      const token = localStorage.getItem("token");
      try {
        const response = await axios.get(
          "http://127.0.0.1:8000/api/chat/history",
          {
            headers: {
              Authorization: `Bearer ${token}`,
            },
          }
        );
        this.chatLogs = response.data.history;
      } catch (err) {
        console.error("Failed to fetch chat history:", err);
      }
    },
    async sendMessage() {
      const token = localStorage.getItem("token");
      try {
        const response = await axios.post(
          "http://127.0.0.1:8000/api/chat/chat",
          { message: this.message },
          {
            headers: { Authorization: `Bearer ${token}` },
          }
        );
        this.chatLogs.push({
          message: this.message,
          response: response.data.response,
        });
        this.message = "";
      } catch (err) {
        console.error("Failed to send message:", err);
      }
    },
    logout() {
      localStorage.removeItem("token");
      this.$router.push("/login");
    },
  },
  mounted() {
    this.fetchChatHistory();
  },
};
</script>
