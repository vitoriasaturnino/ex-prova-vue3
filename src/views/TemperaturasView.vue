<template>
  <div>
    <div class="temperaturas">
      <h1>Tabela de Temperaturas</h1>
      <table>
        <thead>
          <tr>
            <th>ID</th>
            <th>Data/Hora</th>
            <th>Medida (°C)</th>
            <th>Sensação</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="temperatura in temperaturas" :key="temperatura.id">
            <td>{{ temperatura.id }}</td>
            <td>{{ formatarDataHora(temperatura.dataHora) }}</td>
            <td>{{ temperatura.medida }}</td>
            <td>{{ getSensacao(temperatura.medida) }}</td>
          </tr>
        </tbody>
      </table>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted } from 'vue';
import axios from 'axios';
import { format } from 'date-fns';

type Temperatura = {
  id: number;
  dataHora: string;
  medida: number;
};

const temperaturas = ref<Temperatura[]>([]);

const fetchTemperaturas = async () => {
  try {
    const response = await axios.get('/temperatura');
    temperaturas.value = response.data;
  } catch (error) {
    console.error('Erro ao buscar temperaturas:', error);
  }
};

const getSensacao = (medida: number): string => {
  return medida > 26 ? 'Quente' : 'Ok';
};

const formatarDataHora = (dataHora: string): string => {
  return format(new Date(dataHora), 'dd/MM/yyyy HH:mm');
};

onMounted(() => {
  fetchTemperaturas();
});
</script>