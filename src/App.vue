<script setup lang="ts">

// <script setup> é a sintaxe da Composition API que dispensa o export default e o return. Tudo que você declara ali já fica disponível no template. lang="ts" ativa o TypeScript.
import { ref, computed } from 'vue';

//ref cria um dado reativo: quando o valor muda, a tela se atualiza sozinha. Em JavaScript, você acessa e altera o valor por .value.

const newTask = ref('');
// newTask = ref('') guarda o texto digitado no input.
const tasks = ref<string[]>([]);
//tasks = ref<string[]>([]) guarda a lista de tarefas. O <string[]> é a tipagem do TypeScript e indica um array de textos.


// NOVO: valor calculado a partir de `tasks`.
// Sempre que a lista mudar, o Vue recalcula sozinho.
const taskCount = computed(() => tasks.value.length);
 
// NOVO: um computed pode usar outro computed.
const summary = computed(() => {
  if (taskCount.value === 0) return 'Nenhuma tarefa por enquanto';
  if (taskCount.value === 1) return '1 tarefa na lista';
  return `${taskCount.value} tarefas na lista`;
});


function addTask() {
  if (newTask.value.trim()) {
    tasks.value.push(newTask.value.trim());
    newTask.value = '';
  }
} //addTask(): faz trim() para remover espaços nas pontas e só adiciona se o texto não estiver vazio. Depois do push, limpa o input.

function removeTask(index: number) {
  tasks.value.splice(index, 1);
}

// removeTask(index): usa splice(index, 1) para remover da lista o item naquela posição.

</script>


<!--template: a interface -->

<template>
  <div class="todo-container">
    <h1>Minha ToDo List</h1>

    <div class="input-section">
      <input v-model="newTask" @keyup.enter="addTask" placeholder="Digite uma nova tarefa" />
      <button @click="addTask">Adicionar</button>
    </div> 
    
    <!-- v-model="newTask" faz o two-way binding: o que você digita 
    atualiza a variável, e se a variável mudar, o input também muda. 
    É por isso que newTask.value = '' limpa o campo. -->

    <!-- @keyup.enter="addTask" é a abreviação de v-on:keyup.enter. Chama a função quando você aperta Enter.
     @click chama a função ao clicar no botão. -->


     
    <!-- NOVO: exibe o resultado do computed -->
    <p class="summary">{{ summary }}</p>

    <ul>
      <li v-for="(task, index) in tasks" :key="index">
        {{ task }}
        <button @click="removeTask(index)">Remover</button>
      </li>
      
      <!-- v-for="(task, index) in tasks" repete o <li> para cada tarefa.
            
      :key="index" é a abreviação de v-bind:key. A key ajuda o Vue a //identificar cada item da lista.
            
      {{ task }} é a interpolação, que exibe o valor no HTML. -->


    </ul>
  </div>
</template>

<!-- <style scoped>: o visual -->

<style scoped lang="css">

/* O scoped faz o CSS valer apenas para este componente, sem vazar para o resto da aplicação. O layout usa flexbox: .input-section deixa input e botão lado a lado, e cada li separa o texto do botão “Remover”. */

.todo-container {
  max-width: 400px;
  margin: 50px auto;
  font-family: Arial, sans-serif;
  text-align: center;
}
.input-section {
  display: flex;
  gap: 10px;
  margin-bottom: 20px;
}
input {
  flex: 1;
  padding: 8px;
}
button {
  padding: 8px 12px;
  cursor: pointer;
}

/* NOVO */
.summary {
  color: #666;
  margin-bottom: 20px;
}

ul {
  list-style: none;
  padding: 0;
}
li {
  display: flex;
  justify-content: space-between;
  margin-bottom: 10px;
}
</style>