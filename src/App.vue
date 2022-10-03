<script setup>
import { ref, onMounted, computed, watch, transformVNodeArgs } from "vue";

const tarefas = ref([]);
const name = ref("");

const input_content = ref("");
const input_category = ref(null);

const tarefas_asc = computed(() =>
  tarefas.value.sort((a, b) => {
    return b.createdAt - a.createdAt;
  })
);

const addTarefa = () => {
  if (input_content.value.trim() === '' || input_category.value === null) {
    return
  }

  tarefas.value.push({
    content: input_content.value,
    category: input_category.value,
    done: false,
    createdAt: new Date().getTime()
  })

}

const removerTarefa = tarefa => {
  tarefas.value = tarefas.value.filter(t => t !== tarefa)
}

watch(tarefas, newVal => {
  localStorage.setItem('tarefas', JSON.stringify(newVal));
}, { deep: true});


watch(name, (newVal) => {
  localStorage.setItem('name', newVal)
})

onMounted(() => {
  name.value = localStorage.getItem('name') || ''
  tarefas.value = JSON.parse(localStorage.getItem('tarefas')) || []
});
</script>

<template>
  <main class="app">
    <section class="saudacoes">
      <h2 class="titulo">
        Ola,
        <input type="text" placeholder="Coloque um Nome" v-model="name" />
      </h2>
    </section>

    <section class="create-tarefas">
      <h3>Criar Afazeres</h3>

      <form @submit.prevent="addTarefa">
        <h4>O que esta na sua lista de tarefas?</h4>
        <input
          type="texto"
          placeholder="Exemplo fazer o almoço"
          v-model="input_content"
        />

        <h4>Escolha uma categoria</h4> 

        <div class="opcoes">
          <label>
            <input
              type="radio"
              name="category"
              value="trabalho"
              v-model="input_category"
            />
            <span class="trabalho"></span>
            <div>Trabalho</div>
          </label>

          <label>
            <input
              type="radio"
              name="category"
              value="pessoal"
              v-model="input_category"
            />
            <span class="pessoal"></span>
            <div>Pessoal</div>
          </label>
        </div> 

        <button type="enviar" value="Adicionar tarefa">ADICIONAR TAREFA</button>
      </form>
    </section>

    <section class="tarefas-lista">
      <h3>LISTA DE TAREFAS</h3>
      <div class="lista">
        <div v-for="tarefas in tarefas_asc" :class="`tarefas-item ${tarefas.done && 'done'}`">

          <label for>
              <input type="checkbox" v-model="tarefas.done" />
              <span :class="`bubble ${tarefas.category}`"> </span>
          </label>

          <div class="tarefa-content">
              <input type="text" v-model="tarefas.content"/>
          </div>

          <div class="actions">
              <button class="removerTarefa" @click="removerTarefa(tarefa)">Deletar</button>

          </div>
        
        
        </div>
      </div>
    </section>
  </main>
</template>
