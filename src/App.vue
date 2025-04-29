<script>
// Dentro de la etiqueta <script> de los componentes de Vue, si usamos la Option API, necesitamos siempre exportar un
// objeto con la configuración del componente.

// Si queremos usar otros componentes, primero necesitamos importarlos.
// Si estamos usando la Options API, necesitamos además de importar el componente, declararlo en el objeto exportado.
// Los componentes importados correctamente, se pueden utilizar como etiquetas en el <template>.
//import { RouterLink } from 'vue-router';
import Home from './pages/Home.vue';
import { logout, subscribeToAuth } from './services/auth';
import MainNav from './components/MainNav.vue';

export default {
  // name permite configurar el nombre del componente. Es opcional.
  name: 'App',
  // components lleva un objeto con los componentes que se van a utilizar en este componente.
  components: { Home, MainNav },
    // data() {
    //     return {
    //         user: {
    //             id: null,
    //             email: null,
    //         }
    //     }
    // },
    // methods: {
    //     // handleLogin(userData) {
    //     //     this.user = userData;
    //     // }
    //     handleLogout() {
    //         logout();
    //     }
    // },
    // async mounted() {
    //     // Nos suscribimos al estado de autenticación.
    //     // TODO: Hacer el perfil del usuario :)
    //     subscribeToAuth(userData => this.user = userData);
    // }
}
</script>

<template>
  <div class="min-h-screen flex flex-col bg-gray-900 text-white">
    <MainNav />
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