<script>
import { nextTick } from 'vue';
import MainH1 from '../components/MainH1.vue';
import { getLastMessages, saveGlobalChatMessage, subscribeToGlobalChatNewMessages } from '../services/global-chat';

export default {
    name: 'GlobalChat',
    components: { MainH1 },
    /*
    En los componentes de Vue que usan la Options API, definen su "state" (sus datos internos que pueden mutar) a través
    de la propiedad "data".
    Esta propiedad debe ser una función que retorne un objeto con los datos del "state" que queremos, y sus valores
    iniciales.
    */
    data: function() {
        return {
            messages: [],
            newMessage: {
                email: '',
                body: '',
            }
        }
    },
    // data: () => {
    //     return {}
    // }
    // data() {
    //     return {

    //     }
    // }
    methods: {
        async sendMessage() {
            try {
                await saveGlobalChatMessage({...this.newMessage});
                // Versión para 'Broadcast'.
                // const message = {
                //     id: this.messages.length + 1,
                //     email: this.newMessage.email,
                //     body: this.newMessage.body,
                //     created_at: new Date(),
                // };
                // await saveGlobalChatMessage(message);
                this.newMessage.body = "";
            } catch (error) {
                // TODO: Manage the error.
                console.error("[GlobalChat.vue sendMessage] Error al enviar el mensaje: ", error);
            }
        },
    },
    async mounted() {
        // Pedimos recibir los nuevos mensajes para agregarlos en el array de mensajes.
        subscribeToGlobalChatNewMessages(async newMessageReceived => {
            this.messages.push(newMessageReceived);
            await nextTick();
            this.$refs.chatContainer.scrollTop = this.$refs.chatContainer.scrollHeight;
        });
        
        // Traemos los mensajes iniciales.
        try {
            this.messages = await getLastMessages();

            // Movemos el scroll del elemento del contenedor del chat al final.
            console.log(this.$refs.chatContainer);
            // nextTick() es una función que nos permite esperar al próximo frame de redibujo de Vue.
            // ¿Por qué lo necesitamos acá?
            // Nuestra intención es que traigamos los mensajes, se agreguen en el DOM, y luego de que se hayan agregado,
            // se calcule el alto del scroll del contenedor del chat, y lo movamos al final.
            // Esos son dos pasos diferentes en cuando al "redraw" del browser. Para que el scroll del contenedor se
            // actualice, necesitamos que el browser renderice primero en el DOM todos los mensajes.
            // Por defecto, Vue no actualiza el DOM instantánmeamente cada vez que modificamos algo del componente.
            // Para optimizar la performance (ya que el "redraw" es de las tareas más intensivas para el browsers), 
            // Vue "agrupa" (batch) varias acciones de modificación del DOM, y las ejecuta todas juntas.
            // En la mayoría de los casos, es el camino ideal para seguir.
            // Pero hay casos, como este, donde antes de realizar otra acción, necesito sí o sí que lo anterior se
            // ejecute. Ahí es donde necesitamos nextTick().
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
    <div class="bg-gray-800 p-8 rounded-2xl shadow-lg w-full max-w-4xl">
      <MainH1 class="text-white text-center mb-6">Chat Global</MainH1>

      <div class="flex gap-8">
        <!-- Mensajes -->
        <div 
          ref="chatContainer"
          class="overflow-y-auto w-2/3 h-[60vh] p-4 border border-gray-600 rounded-lg bg-gray-700 text-white"
        >
          <h2 class="sr-only">Lista de Mensajes</h2>
          <ul>
            <li v-for="message in messages" :key="message.id" class="flex flex-col gap-2 mb-4">
              <div class="text-sm font-semibold text-blue-400">{{ message.email }} dijo:</div>
              <div class="text-lg">{{ message.body }}</div>
              <div class="text-xs text-gray-500">{{ message.created_at }}</div>
            </li>
          </ul>
        </div>

        <!-- Enviar Mensaje -->
        <div class="w-1/3 p-4 bg-gray-700 rounded-lg">
          <h2 class="text-2xl text-white mb-4">Enviar un mensaje</h2>

          <form action="#" @submit.prevent="sendMessage">
            <div class="mb-4">
              <label for="email" class="block mb-2 text-gray-300">Email:</label>
              <input
                type="email"
                id="email"
                class="w-full px-4 py-2 border border-gray-600 rounded bg-gray-800 text-white focus:outline-none focus:ring-2 focus:ring-blue-500"
                v-model="newMessage.email"
                placeholder="Ingresa tu correo"
              >
            </div>

            <div class="mb-4">
              <label for="body" class="block mb-2 text-gray-300">Mensaje:</label>
              <textarea
                id="body"
                class="w-full px-4 py-2 border border-gray-600 rounded bg-gray-800 text-white focus:outline-none focus:ring-2 focus:ring-blue-500"
                v-model="newMessage.body"
                placeholder="Escribe tu mensaje"
              ></textarea>
            </div>

            <button
              type="submit"
              class="w-full bg-blue-600 hover:bg-blue-500 text-white font-semibold py-2 rounded-lg transition duration-200"
            >
              Enviar
            </button>
          </form>
        </div>
      </div>
    </div>
  </div>
</template>
