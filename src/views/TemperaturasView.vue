<template>
  <div cass="searchTemperatura">
    <h1>Buscar Temperaturas</h1>
      <form @submit.prevent="buscarTemperatura">
        <div>
          <label for="buscaDataHora">Data/Hora:</label>
          <input type="datetime-local" id="buscaDataHora" v-model="busca.dataHora">
        </div>
        <div>
          <label for="buscaMedida">Medida (°C):</label>
          <input type="number" id="buscaMedida" v-model.number="busca.medida">
        </div>
        <button type="submit">Buscar Temperatura</button>
      </form>
  </div>

  <br>

  <div class="temperaturas">
    <div v-if="temperaturas.length > 0">
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
      <br>
    </div>
    <div v-else>
      Nenhum registro encontrado de acordo com a busca realizada =()
    </div>
  </div>

    <br>

    <div class="createTemperatura">
      <h1>Cadastrar nova Temperatura</h1>
      <form @submit.prevent="submitNovaTemperatura">
        <div>
          <label for="dataHora">Data/Hora:</label>
          <input type="datetime-local" id="dataHora" v-model="novaTemperatura.dataHora" required>
        </div>
        <div>
          <label for="medida">Medida (°C):</label>
          <input type="number" id="medida" v-model.number="novaTemperatura.medida" required>
        </div>
        <button type="submit">Cadastrar Temperatura</button>
      </form>
    </div>

    <br>
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
type NovaTemperatura = { dataHora: string, medida: number };
type Busca = { dataHora: string, medida: number | null };

const temperaturas = ref<Temperatura[]>([]);
const novaTemperatura = ref<NovaTemperatura>({ dataHora: '', medida: 0 });
const busca = ref<Busca>({ dataHora: '', medida: null });

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

const submitNovaTemperatura = async () => {
  try {
    await axios.post('/temperatura', novaTemperatura.value);
    fetchTemperaturas(); // Atualiza a lista
    novaTemperatura.value = { dataHora: '', medida: 0 }; // Limpa os campos
  } catch (error) {
    console.error('Erro ao cadastrar temperatura:', error);
  }
};

// a requisição está diferente do que o back-end está esperando
const buscarTemperatura = async () => {
  try {
    const response = await axios.get('/temperatura', { 
      params: { 
        dataHora: busca.value.dataHora, 
        medida: busca.value.medida 
      } 
    });
    temperaturas.value = response.data;
  } catch (error) {
    console.error('Erro ao buscar temperatura:', error);
  }
};

onMounted(() => {
  fetchTemperaturas();
});
</script>