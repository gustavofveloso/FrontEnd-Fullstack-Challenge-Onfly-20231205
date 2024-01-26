<template>
  <q-layout>
    <q-page-container>
      <q-page class="flex">
        <div :class="{ 'none': $q.screen.lt.md }" class="w-60 flex justify-center">
          <q-img width="70%" src="../assets/login-background-image.png" alt="Gestão de Despesas - Background" />
        </div>
        <div :class="{ 'w-100': $q.screen.lt.md}" class="w-40 flex column justify-center content-center bg-primary ">
          <div class="flex justify-center content-center">
            <q-img width="40%" src="../assets/onfly-logo.png" alt="Onfly - Logo" />
          </div>

          <q-form :class="{ 'w-90': $q.screen.lt.sm }" class="q-gutter-md mt-20 flex column" @submit="handleSubmit">
            <q-input type="email" bg-color="white" color="grey-7" label="Email" outlined v-model="email" lazy-rules required>
              <template v-slot:prepend>
                <q-icon name="mail" />
              </template>
            </q-input>
            <q-input type="password" bg-color="white" color="grey-7" label="Senha" outlined v-model="password" lazy-rules required>
              <template v-slot:prepend>
                <q-icon name="password" />
              </template>
            </q-input>
            <div :class="{ 'column': $q.screen.lt.sm }" class="flex">
              <q-btn icon="login" type="submit" color="blue-8" label="Entrar" size="md" />
              <q-btn :class="{ 'font-12': $q.screen.lt.sm }" flat color="white" label="Ainda não possui uma conta?" to="/register" />
            </div>
          </q-form>
        </div>
      </q-page>
    </q-page-container>
  </q-layout>
</template>

<script setup lang="ts">
import { ref } from 'vue';
import { useRouter } from 'vue-router';
import { login } from '../api/auth';
import { useQuasar } from 'quasar';

const email = ref('');
const password = ref('');

const router = useRouter();
const $q = useQuasar();

const handleSubmit = () => {
  login({ email: email.value, password: password.value }).then(() => {
    $q.notify({
      spinner: true,
      color: 'green-6',
      message: 'Acessando...',
      timeout: 200,
    });
    setTimeout(() => {
      router.push({ name: 'home' });
    }, 500);
  }).catch((err) => {
    $q.notify({
      color: 'red-6',
      message: 'Dados de acesso inválidos.',
      icon: 'error',
    });
    console.log(err);
  });
};
</script>
