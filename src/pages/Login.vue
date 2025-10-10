
<template>
  <v-container fluid class="d-flex align-center justify-center fill-height">
    <v-card
      class="pa-8 rounded-xl"
      elevation="8"
      max-width="400"
    >
      <v-card-title class="text-h5 text-center mb-4">
        Iniciar Sesión
      </v-card-title>

      <v-alert type="error" dismissible class="mb-4">
      {{ errorMessage }}
      </v-alert>

      <v-card-text>
        <v-text-field
          label="Correo electrónico"
          v-model="email"
          prepend-inner-icon="mdi-email"
          variant="outlined"
          density="comfortable"
          class="mb-4"
        />

        <v-text-field
          label="Contraseña"
          v-model="password"
          type="password"
          prepend-inner-icon="mdi-lock"
          variant="outlined"
          density="comfortable"
        />
      </v-card-text>

      <v-card-actions class="d-flex flex-column">
        <v-btn 
          block @click="login"
          color="primary"
          class="mb-2"
          size="large" 
        >
          Entrar
        </v-btn>

        <v-btn
          variant="text"
          size="small"
          color="secondary"
        >
          ¿Olvidaste tu contraseña?
        </v-btn>
      </v-card-actions>
    </v-card>
  </v-container>
</template>

<script setup lang="ts">
import { ref } from 'vue';
import { useRouter } from 'vue-router';
import axios from '../config/axios';
import { AxiosError } from 'axios';

const email = ref('');
const password = ref('');

const errorMessage = ref('');

const router = useRouter();

const login = async () => {
  try {
    if (!email.value || !password.value) {
      errorMessage.value = 'Por favor, completa todos los campos';
      return;
    }

    const response = await axios.post('/login', {
      email: email.value,
      password: password.value,
    });

    if(response.data.acceso == "Ok")
    {
      const token = response.data.token;
      localStorage.setItem('token', token);

      await router.push('/home');
    }else{
      errorMessage.value = response.data.error;
    }
    

    
  } catch (err) {

    
    if (err instanceof AxiosError) {
      if (err.response && err.response.data) {
        errorMessage.value = err.response.data.message || 'Error al iniciar sesión';
      } else {
        errorMessage.value = 'Error de conexión con el servidor.';
      }
    } else {
      errorMessage.value = 'Ocurrió un error inesperado. Inténtalo nuevamente.';
    }
    
  } 
};
</script>

<style scoped>
.fill-height {
  height: 100vh;
  background: linear-gradient(135deg, #794bc4, #7d66d3);
}
</style>