<script>
import { subscribeToAuth, logout } from '../services/auth';

export default {
    name: 'MainNav',
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
            // Redireccionamos a la pantalla del login.
            // Para redireccionar programáticamente podemos usar el método "push" del router, que podemos acceder con
            // this.$router.
            this.$router.push('/ingresar');
        }
    },
    mounted() {
        subscribeToAuth(newUserData => this.user = newUserData);
    }
}
</script>

<template>
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
                    <router-link to="/mi-perfil" class="hover:text-blue-400 transition">Mi perfil</router-link>
                </li>
                <li>
                    <button @click="handleLogout" class="hover:text-blue-400 transition">
                        {{ user.email }} (Cerrar sesión)
                    </button>
                </li>
            </template>

        </ul>
    </nav>
</template>