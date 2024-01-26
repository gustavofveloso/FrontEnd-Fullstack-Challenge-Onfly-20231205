<template>
  <q-page class="m-auto">
    <q-card class="no-shadow" bordered>
      <q-card-section>
        <div class="text-h6 text-grey-8">
          Despesas
          <q-btn label="Adicionar Despesa" class="float-right text-capitalize text-indigo-8 shadow-3" icon="add"
            @click="handleAdd" />
        </div>
      </q-card-section>
      <q-separator></q-separator>
      <q-card-section class="q-pa-none">
        <table-component v-if="paginatedData && !loading" :paginatedData="paginatedData" :fetchData="fetchData" />
        <table-skeleton :rows="rowsPerPage" v-else />
      </q-card-section>
    </q-card>
  </q-page>
</template>

<script setup lang="ts">
import { ref } from 'vue';
import { useQuasar } from 'quasar';
import { getExpenses, createExpense, ExpensePaginatedResponse } from '../api/expenses';
import { notifySucess, notifyError } from '../utils/notify';
import TableComponent from '../components/TableComponent.vue';
import TableSkeleton from '../components/TableSkeleton.vue';


const paginatedData = ref<ExpensePaginatedResponse | null>(null);
const loading = ref<boolean>(false);
const rowsPerPage = ref<number>(10);

const $q = useQuasar()

const fetchData = async (page = 1, limit = 10) => {
  rowsPerPage.value = limit
  loading.value = true
  await getExpenses(page, limit)
    .then((res) => {
      paginatedData.value = res
      loading.value = false
    })
    .catch((err) => {
      notifyError($q, 'Error fetching expenses')
      console.log(err);
    });
}

const handleAdd = async () => {
  const newExpense = {
    description: 'Viagem para o Canadá',
    date: new Date().toISOString().split('T')[0],
    value: 1
  };

  await createExpense(newExpense)
    .then(expense => {
      fetchData(paginatedData.value?.meta.current_page, paginatedData.value?.meta.per_page)
      notifySucess($q, 'Despesa adicionada com sucesso!')
      console.log('Despesa adicionada com sucesso:', expense);
    })
    .catch(error => {
      notifyError($q, 'Erro ao adicionar despesa :(')
      console.error('Erro ao adicionar despesa:', error);
    });
};

fetchData()
</script>
