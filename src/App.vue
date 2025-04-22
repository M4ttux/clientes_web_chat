<script>
// Dentro de la etiqueta <script> de los componentes de Vue, si usamos la Option API, necesitamos siempre exportar un
// objeto con la configuración del componente.

// Si queremos usar otros componentes, primero necesitamos importarlos.
// Si estamos usando la Options API, necesitamos además de importar el componente, declararlo en el objeto exportado.
// Los componentes importados correctamente, se pueden utilizar como etiquetas en el <template>.
import { RouterLink } from 'vue-router';
import Home from './pages/Home.vue';
import { logout, subscribeToAuth } from './services/auth';

export default {
  // name permite configurar el nombre del componente. Es opcional.
  name: 'App',
  // components lleva un objeto con los componentes que se van a utilizar en este componente.
  components: { Home },
  data() {
    return {
      user: {
        id: null,
        email: null,
      }
    };
  },
  methods: {
    handleLogout() {
      logout();
    }
  },
  async mounted() {
    subscribeToAuth(userData => {
      this.user = userData;
    });
  }

}
</script>

<template>
  <div class="min-h-screen flex flex-col bg-gray-900 text-white">
    <!-- NAVBAR -->
    <nav class="flex justify-between items-center px-6 py-4 bg-gray-800 shadow-md">
      <router-link to="/" class="text-2xl font-bold text-white">DV Social</router-link>
      <ul class="flex gap-6 text-sm font-medium">
        <li>
          <router-link to="/" class="hover:text-blue-400 transition">Home</router-link>
        </li>
        <template v-if="user.id === null">
          <li>
            <router-link to="/ingresar" class="hover:text-blue-400 transition">Iniciar Sesión</router-link>
          </li>
          <li>
            <router-link to="/registro" class="hover:text-blue-400 transition">Registrarse</router-link>
          </li>
        </template>
        <template v-else>
          <li>
            <router-link to="/chat-global" class="hover:text-blue-400 transition">Chat Global</router-link>
          </li>
          <li>
            <form action="#" @submit.prevent="handleLogout">
              <button type="submit">{{ user.email }} (Cerrar sesión)</button>
            </form>
          </li>
        </template>

      </ul>
    </nav>

    <!-- CONTENIDO PRINCIPAL -->
    <main class="flex-grow container mx-auto px-4 py-6">
      <router-view />
    </main>

    <!-- FOOTER -->
    <footer class="bg-gray-800 py-4 text-center text-sm text-gray-400 border-t border-gray-700">
      Da Vinci &copy; 2025
    </footer>
  </div>
</template>