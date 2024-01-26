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

          <q-form :class="{ 'w-90': $q.screen.lt.sm }" class="q-gutter-md mt-20" @submit="handleSubmit">
            <q-input bg-color="white" color="grey-7" outlined v-model="name" label="Nome" lazy-rules required>
              <template v-slot:prepend>
                <q-icon name="badge" />
              </template>
            </q-input>
            <q-input bg-color="white" color="grey-7" outlined v-model="email" type="email" label="Email" lazy-rules required>
              <template v-slot:prepend>
                <q-icon name="mail" />
              </template>
            </q-input>
            <q-input
              bg-color="white"
              color="grey-7"
              outlined
              type="password"
              v-model="password"
              label="Senha"
              lazy-rules
              required
            >
              <template v-slot:prepend>
                <q-icon name="password" />
              </template>
            </q-input>
            <q-input
              bg-color="white"
              color="grey-7"
              outlined
              filled
              v-model="passwordConfirmation"
              :type="isPwd ? 'password' : 'text'"
              label="Confirme sua Senha"
              lazy-rules
              required
            >
              <template v-slot:prepend>
                <q-icon name="password" />
              </template>
              <template v-slot:append>
                <q-icon
                  :name="isPwd ? 'visibility_off' : 'visibility'"
                  class="cursor-pointer"
                  @click="isPwd = !isPwd"
                />
              </template>
            </q-input>

            <div :class="{ 'column': $q.screen.lt.sm }" class="flex">
              <q-btn icon="login" label="Cadastrar" color="blue-8" type="submit" />
              <q-btn :class="{ 'font-12': $q.screen.lt.sm }" flat color="white" label="Já possui uma conta?" to="/login" />
            </div>
          </q-form>
        </div>
      </q-page>
    </q-page-container>
  </q-layout>
</template>

<script setup lang="ts">
import { ref, computed } from 'vue';
import { useRouter } from 'vue-router';
import { useQuasar } from 'quasar';
import { register } from '../api/auth';

const name = ref('');
const email = ref('');
const password = ref('');
const passwordConfirmation = ref('');
const isPwd = ref(true);

const router = useRouter();
const $q = useQuasar();

const passwordsMatch = computed(() => {
  return password.value === passwordConfirmation.value;
});

const validPasswordLength = computed(() => {
  return password.value.length >= 8;
});

const handleSubmit = () => {
  if (!passwordsMatch.value) {
    $q.notify({
      color: 'red-6',
      message: 'As senhas não correspondem.',
      icon: 'error',
    });
    return;
  }

  if (!validPasswordLength.value) {
    $q.notify({
      color: 'red-6',
      message: 'A senha deve ter pelo menos 8 caracteres.',
      icon: 'error',
    });
    return;
  }

  register({
    name: name.value,
    email: email.value,
    password: password.value,
    password_confirmation: passwordConfirmation.value,
  })
    .then(() => {
      $q.notify({
        color: 'green-6',
        message: 'Registro realizado com sucesso!',
      });
      setTimeout(() => {
        router.push({ name: 'home' });
      }, 500);
    }).catch((err) => {
      $q.notify({
        color: 'red-6',
        message: 'Verifique as informações.',
        icon: 'error',
      });
      console.log(err);
    });
};
</script>
