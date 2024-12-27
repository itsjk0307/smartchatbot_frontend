<!-- eslint-disable vue/multi-word-component-names -->
<template>
  <div class="flex items-center justify-center min-h-screen bg-gray-100">
    <div class="w-full max-w-md p-8 bg-white rounded shadow">
      <h1 class="text-2xl font-bold mb-6 text-center">Login</h1>
      <form @submit.prevent="login">
        <div class="mb-4">
          <label class="block text-gray-700">Username:</label>
          <input
            v-model="username"
            type="text"
            class="w-full px-3 py-2 border rounded focus:outline-none focus:ring"
            required
          />
        </div>
        <div class="mb-6">
          <label class="block text-gray-700">Password:</label>
          <input
            v-model="password"
            type="password"
            class="w-full px-3 py-2 border rounded focus:outline-none focus:ring"
            required
          />
        </div>
        <button
          type="submit"
          class="w-full bg-blue-500 text-white py-2 px-4 rounded hover:bg-blue-600"
        >
          Login
        </button>
      </form>
    </div>

    <!-- Modal -->
    <div v-if="showModal" class="fixed z-10 inset-0 overflow-y-auto">
      <div class="flex items-center justify-center min-h-screen">
        <div class="bg-white rounded-lg shadow p-6 w-1/3">
          <h2 class="text-red-600 text-xl font-bold mb-4">Error</h2>
          <p class="text-gray-600 mb-4">{{ modalMessage }}</p>
          <button
            class="bg-red-500 text-white px-4 py-2 rounded hover:bg-red-600"
            @click="closeModal"
          >
            Close
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";

export default {
  data() {
    return {
      username: "",
      password: "",
      showModal: false,
      modalMessage: "",
    };
  },
  methods: {
    showError(message) {
      this.modalMessage = message;
      this.showModal = true;
    },
    closeModal() {
      this.showModal = false;
      this.modalMessage = "";
    },
    async login() {
      try {
        const response = await axios.post(
          "http://127.0.0.1:8000/api/auth/login",
          {
            username: this.username,
            password: this.password,
          }
        );
        const token = response.data.access_token;
        localStorage.setItem("token", token);
        this.$router.push("/chat");
      } catch (err) {
        this.showError("Invalid username or password.");
      }
    },
  },
};
</script>
