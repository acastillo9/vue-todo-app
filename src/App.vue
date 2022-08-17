<script setup>
import { ref, computed, onMounted } from "vue";

const newTodo = ref("");
const hideCompleted = ref(false);
const todos = ref([]);

const filteredTodos = computed(() => {
  return hideCompleted.value ? todos.value.filter((t) => !t.completed) : todos.value;
});

async function addTodo() {
  const todo = { id: todos.value.length + 1, title: newTodo.value };
  const res = await fetch(
    `https://jsonplaceholder.typicode.com/todos`,
    { method: 'POST', body: JSON.stringify(todo) }
  )
  await res.json()

  todos.value.push(todo);
  newTodo.value = "";
}

async function removeTodo(todo) {
  const res = await fetch(
    `https://jsonplaceholder.typicode.com/todos/${todo.id}`,
    { method: 'DELETE' }
  )
  await res.json()

  todos.value = todos.value.filter((t) => t !== todo);
}

async function fetchTodos() {
  todos.value = [];
  const res = await fetch(
    `https://jsonplaceholder.typicode.com/todos`
  )
  todos.value = await res.json()
}

onMounted(() => {
  fetchTodos()
})
</script>

<template>
  <h1>ToDo App</h1>
  <form @submit.prevent="addTodo">
    <input v-model="newTodo" />
    <button>Add Todo</button>
  </form>
  <ul>
    <li v-for="todo in filteredTodos" :key="todo.id">
      <input type="checkbox" v-model="todo.completed" />
      <span :class="{ done: todo.completed }">{{ todo.title }}</span>
      <button @click="removeTodo(todo)">X</button>
    </li>
  </ul>
  <button @click="hideCompleted = !hideCompleted">
    {{ hideCompleted ? "Show all" : "Hide completed" }}
  </button>
</template>

<style>
.done {
  text-decoration: line-through;
}
</style>
