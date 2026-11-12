<script setup lang="ts">

// <script setup> é a sintaxe da Composition API que dispensa o export default e o return. Tudo que você declara ali já fica disponível no template. lang="ts" ativa o TypeScript.
import { ref, computed } from 'vue';

//ref cria um dado reativo: quando o valor muda, a tela se atualiza sozinha. Em JavaScript, você acessa e altera o valor por .value.



// NOVO: cada tarefa agora é um objeto, e não mais apenas um texto.
interface Task {
  id: number;
  text: string;
  done: boolean;
}


const newTask = ref('');
// newTask = ref('') guarda o texto digitado no input.
const tasks = ref<Task[]>([]);
//tasks = ref<Task[]>([]) guarda a lista de tarefas. O <Task[]> é a tipagem do TypeScript e indica um array de objetos Task.


// valor calculado a partir de `tasks`.
// Sempre que a lista mudar, o Vue recalcula sozinho.

// NOVO: gerador simples de ids únicos (não precisa ser reativo).
let nextId = 1;

const totalCount = computed(() => tasks.value.length);


 
// NOVO: conta só as tarefas concluídas.
const completedCount = computed(() => tasks.value.filter((task) => task.done).length);
 
// ATUALIZADO: o resumo agora usa os dois contadores.
const summary = computed(() => {
  if (totalCount.value === 0) return 'Nenhuma tarefa por enquanto';
  return `${completedCount.value} de ${totalCount.value} concluídas`;
});


function addTask() {
  const text = newTask.value.trim();
  if (text) {
    // ATUALIZADO: agora adicionamos um objeto, com id e done.
    tasks.value.push({ id: nextId++, text, done: false });
    newTask.value = '';
  }
} 

// ATUALIZADO: remove pelo id, e não mais pela posição.
function removeTask(id: number) {
  tasks.value = tasks.value.filter((task) => task.id !== id);
}
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


     
    <!-- exibe o resultado do computed -->
    <p class="summary">{{ summary }}</p>


    <ul>
      <!-- ATUALIZADO: a key agora é o id único da tarefa -->
      <li v-for="task in tasks" :key="task.id">
        <!-- NOVO: checkbox ligado ao campo `done` da tarefa -->
        <label class="task-label">
          <input type="checkbox" v-model="task.done" />
          <!-- NOVO: aplica a classe `done` só quando a tarefa está concluída -->
          <span :class="{ done: task.done }">{{ task.text }}</span>
        </label>
        <button @click="removeTask(task.id)">Remover</button>
      </li>
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

/* NOVO */
.task-label {
  display: flex;
  align-items: center;
  gap: 8px;
  cursor: pointer;
}
/* NOVO: visual de tarefa concluída */
.done {
  text-decoration: line-through;
  color: #999;
}
</style>