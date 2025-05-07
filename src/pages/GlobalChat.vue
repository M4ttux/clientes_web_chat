<script>
import { nextTick } from 'vue';
import MainH1 from '../components/MainH1.vue';
import { getLastMessages, saveGlobalChatMessage, subscribeToGlobalChatNewMessages } from '../services/global-chat';
import { subscribeToAuth } from '../services/auth';
import MainLoader from '../components/MainLoader.vue';

export default {
  name: 'GlobalChat',
  components: { MainH1, MainLoader },

  data: function () {
    return {
      messages: [],
      loadingMessages: true,

      newMessage: {
        body: '',
      },

      user: {
        id: null,
        email: null,
        bio: null,
        display_name: null,
        career: null,
      }
    }
  },

  methods: {
    async sendMessage() {
      try {
        await saveGlobalChatMessage({
          body: this.newMessage.body,
          user_id: this.user.id,
          email: this.user.email,
        });

        this.newMessage.body = '';
      } catch (error) {
        // TODO: Manage the error.
        console.error("[GlobalChat.vue sendMessage] Error al enviar el mensaje: ", error);
      }
    },
  },

  async mounted() {
    subscribeToAuth(newUser => this.user = newUser);
    subscribeToGlobalChatNewMessages(async newMessageReceived => {
      this.messages.push(newMessageReceived);
      await nextTick();
      this.$refs.chatContainer.scrollTop = this.$refs.chatContainer.scrollHeight;
    });

    try {
      this.messages = await getLastMessages();
      this.loadingMessages = false;
      console.log(this.$refs.chatContainer);
      await nextTick();
      this.$refs.chatContainer.scrollTop = this.$refs.chatContainer.scrollHeight;
    } catch (error) {
      // TODO:
    }
  }
}
</script>

<template>
  <div class="flex items-center justify-center bg-gray-900 min-h-full px-4">
    <div class="bg-gray-800 p-8 rounded-2xl shadow-lg w-full max-w-6xl">
      <MainH1 class="text-white text-center mb-6">Chat Global</MainH1>

      <div class="flex gap-8">
        <!-- Mensajes -->
        <div ref="chatContainer"
          class="overflow-y-auto w-9/12 h-[60vh] p-4 border border-gray-600 rounded-lg bg-gray-700 text-white">
          <h2 class="sr-only">Lista de Mensajes</h2>

          <ul v-if="!loadingMessages">
            <li v-for="message in messages" class="flex flex-col gap-2 mb-4">
              <div>
                <router-link :to="`/usuario/${message.user_id}`" class="text-blue-400 font-semibold underline">
                  {{ message.email }}
                </router-link>
                dijo:
              </div>
              <div class="text-lg">{{ message.body }}</div>
              <div class="text-xs text-gray-200/40">{{ message.created_at }}</div>
            </li>
          </ul>

          <div v-else>
            <MainLoader />
          </div>
        </div>

        <!-- Enviar Mensaje -->
        <div class="w-3/12 p-4 bg-gray-700 rounded-lg">
          <h2 class="mb-4 text-2xl text-white">Enviar un mensaje</h2>

          <form action="#" @submit.prevent="() => sendMessage()">
            <div class="mb-4">
              <div class="block mb-2 text-gray-300">Email:</div>
              <div class="font-bold text-white">{{ user.email }}</div>
            </div>

            <div class="mb-4">
              <label for="body" class="block mb-2 text-gray-300">Mensaje:</label>
              <textarea id="body"
                class="w-full px-4 py-2 border border-gray-600 rounded bg-gray-800 text-white focus:outline-none focus:ring-2 focus:ring-blue-500"
                v-model="newMessage.body"></textarea>
            </div>

            <button type="submit"
              class="w-full bg-blue-600 hover:bg-blue-500 text-white font-semibold py-2 rounded-lg transition duration-200">
              Enviar
            </button>
          </form>
        </div>
      </div>
    </div>
  </div>
</template>
