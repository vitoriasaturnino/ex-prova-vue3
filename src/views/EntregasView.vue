<template>
  <div class="entrega">
    <h1>Gerenciador de Entregas</h1>
    <form @submit.prevent="createEntrega">

      <div>
        <label for="ent_descricao">Descrição da Entrega: </label>
        <input type="text" id="ent_descricao" v-model="newEntrega.ent_descricao">
      </div>

      # dois campos date time

      <div>
        <label for="ent_peso">Peso: </label>
        <input type="text" id="ent_peso" v-model="newEntrega.ent_peso">
      </div>

      <div>
        <label for="ent_observacoes">Observações: </label>
        <input type="text" id="ent_observacoes" v-model="newEntrega.ent_observacoes">
      </div>
      
      <button type="submit">Salvar</button>
      <p v-if="erroSalvar">{{ erroSalvar }}</p>

      <!-- ainda não sei o que fazer
      <div>
        <label for="findEntrega">Termo para consulta: </label>
        <input type="text" id="findEntrega" v-model="findEntrega">
      </div>
      <div>
        <label for="versaoConsulta">Versão para consulta: </label>
        <input type="text" id="versaoConsulta" v-model="versaoConsulta">
      </div>
      <button @click="consultarentrega">Buscar</button>
    </form>
    -->

    <ul v-if="listAllEntregas && entregas.length > 0">
      <li v-for="entrega in entregas" :key="entrega.id">
        <span> ID: {{ entrega.id }} </span>
        <span> Termo: {{ entrega.ent_descricao }} -</span>
        <!-- talvez entrem os campos de data aqui-->
        <span> Significado: {{ entrega.ent_peso }} -</span>
        <span> Versão: {{ entrega.ent_observacoes }} </span>
      </li>
    </ul>

    <!--
    <p v-else-if="listAllEntregas && entregas.length === 0 && findEntrega && versaoConsulta">
      Nenhum vocábulo foi encontrado para os critérios fornecidos.
    </p>
    <p v-else>{{ mensagemConsulta }}</p>
    -->
  </div>
</template>

<script setup lang="ts">
import { onMounted, ref } from 'vue';
import axios from 'axios';

const entregas = ref([]);
const newEntrega = ref({ ent_descricao: '', ent_peso: '', ent_observacoes: '' });
const erroSalvar = ref('');
//const findEntrega = ref('');
//const versaoConsulta = ref('');
const listAllEntregas = ref(true);
//const mensagemConsulta = ref('');

async function reload() {
  try {
    entregas.value = (await axios.get("entrega")).data;
  } catch (ex) {
    erroSalvar.value = (ex as Error).message;
  }
}

async function createEntrega() {
  try {
    if (!newEntrega.value.ent_descricao || !newEntrega.value.ent_peso || !newEntrega.value.ent_observacoes) {
      erroSalvar.value = "É preciso preencher todos os campos!.";
      return;
    }

    await axios.post("entrega", {
      ent_descricao: newEntrega.value.ent_descricao,
      ent_peso: newEntrega.value.ent_peso,
      ent_observacoes: newEntrega.value.ent_observacoes
    });

    newEntrega.value = { ent_descricao: '', ent_peso: '', ent_observacoes: '' };
    erroSalvar.value = '';

    await reload();
  } catch (ex) {
    erroSalvar.value = (ex as Error).message;
  }
}

/* ainda não sei o que fazer
async function consultarentrega() {
  try {
    const response = await axios.get(`entrega/${findEntrega.value}/${versaoConsulta.value}`);
    entregas.value = response.data;

    listAllEntregas.value = true;
    if (entregas.value.length === 0 && findEntrega && versaoConsulta) {
      mensagemConsulta.value = "Nenhum vocábulo foi encontrado para os critérios fornecidos.";
    } else {
      mensagemConsulta.value = '';
    }
  } catch (ex) {
    erroSalvar.value = (ex as Error).message;
  }
}*/



onMounted(() => {
  reload();
});
</script>