<script>
import MainH1 from "../components/MainH1.vue";
import { register } from "../services/auth";

export default {
  name: "Register",
  components: { MainH1 },
  data() {
    return {
      user: {
        email: "",
        password: "",
      },
    };
  },
  methods: {
    async handleSubmit() {
      try {
        await register(this.user.email, this.user.password);
      } catch (error) {
        console.error(
          "[Register handleSubmit] Error al registrar el usuario: ",
          error
        );
      }
    },
  },
};
</script>

<template>
  <div class="flex items-center justify-center bg-gray-900 min-h-full px-4">
    <div class="bg-gray-800 p-8 rounded-2xl shadow-lg w-full max-w-md">
      <MainH1>Crear una Cuenta</MainH1>

      <form action="#" @submit.prevent="handleSubmit">
        <div class="mb-4">
          <label for="email" class="block mb-2 text-gray-300">Email</label>
          <input
            v-model="user.email"
            type="email"
            id="email"
            class="w-full px-4 py-2 rounded-lg bg-gray-700 text-white border border-gray-600 focus:outline-none focus:ring-2 focus:ring-blue-500"
          />
        </div>
        <div class="mb-6">
          <label for="password" class="block mb-2 text-gray-300"
            >Contraseña</label
          >
          <input
            v-model="user.password"
            type="password"
            id="password"
            class="w-full px-4 py-2 rounded-lg bg-gray-700 text-white border border-gray-600 focus:outline-none focus:ring-2 focus:ring-blue-500"
          />
        </div>
        <button
          type="submit"
          class="w-full bg-blue-600 hover:bg-blue-500 text-white font-semibold py-2 rounded-lg transition duration-200"
        >
          Crear Cuenta
        </button>
      </form>
      <div class="mt-4 text-center text-gray-300">
        <span>Ya tienes cuenta? </span>
        <router-link to="/ingresar" class="text-blue-500 hover:text-blue-400"
          >Iniciar sesión</router-link
        >
      </div>
    </div>
  </div>
</template>
