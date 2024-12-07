<template>
  <div id="app">
    <h1>Telegram WebApp Game</h1>
    <div v-if="loading">Загрузка...</div>
    <div v-else>
      <p><strong>Ваш Telegram ID:</strong> {{ telegramId }}</p>
      <p><strong>Ваш Score:</strong> {{ score }}</p>
      <button @click="collect">Collect</button>
    </div>
  </div>
</template>

<script>
import axios from "axios";

export default {
  data() {
    return {
      telegramId: null,
      score: 0,
      loading: true,
    };
  },
  methods: {
    async collect() {
      try {
        const response = await axios.post("http://localhost:3000/collect", {
          telegram_id: this.telegramId,
        });
        this.score = response.data.collected_value;
      } catch (error) {
        console.error("Ошибка при сборе очков:", error);
      }
    },
    async initializeUser() {
      try {
        // Получаем Telegram ID
        const tg = window.Telegram.WebApp.initDataUnsafe.user;
        if (!tg || !tg.id) {
          console.error("Не удалось получить Telegram ID");
          return;
        }
        this.telegramId = tg.id;

        // Отправляем данные на сервер
        const response = await axios.post("http://localhost:3000/init", {
          telegram_id: this.telegramId,
        });
        this.score = response.data.collected_value || 0;
      } catch (error) {
        console.error("Ошибка инициализации пользователя:", error);
      } finally {
        this.loading = false;
      }
    },
  },
  mounted() {
    this.initializeUser();
  },
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  text-align: center;
  margin-top: 50px;
}
button {
  padding: 8px 16px;
  margin: 8px;
}
</style>
