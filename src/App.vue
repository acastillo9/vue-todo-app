<script setup>
import { ref, computed, onMounted, watch } from "vue";

const newTodo = ref("");
const hideCompleted = ref(false);
const todos = ref([]);
const selectedTodoId = ref(0);
const selectedTodoData = ref(null);

const filteredTodos = computed(() => {
  return hideCompleted.value
    ? todos.value.filter((t) => !t.completed)
    : todos.value;
});

async function addTodo() {
  const todo = { id: todos.value.length + 1, title: newTodo.value };
  const res = await fetch(`https://jsonplaceholder.typicode.com/todos`, {
    method: "POST",
    body: JSON.stringify(todo),
  });
  await res.json();

  todos.value.push(todo);
  newTodo.value = "";
}

async function removeTodo(todo) {
  const res = await fetch(
    `https://jsonplaceholder.typicode.com/todos/${todo.id}`,
    { method: "DELETE" }
  );
  await res.json();

  todos.value = todos.value.filter((t) => t !== todo);
}

async function fetchTodos() {
  todos.value = [];
  const res = await fetch(`https://jsonplaceholder.typicode.com/todos`);
  todos.value = await res.json();
}

function selectTodo(todoId) {
  selectedTodoId.value = todoId;
}

async function fetchTodoData() {
  selectedTodoData.value = null;
  const res = await fetch(
    `https://jsonplaceholder.typicode.com/todos/${selectedTodoId.value}`
  );
  selectedTodoData.value = await res.json();
}

watch(selectedTodoId, fetchTodoData);

onMounted(() => {
  fetchTodos();
});
</script>

<template>
  <h1>ToDo App</h1>
  <form @submit.prevent="addTodo">
    <input v-model="newTodo" />
    <button>Add Todo</button>
  </form>
  <p>{{ selectedTodoData }}</p>
  <ul>
    <li v-for="todo in filteredTodos" :key="todo.id">
      <input type="checkbox" v-model="todo.completed" />
      <span
        :class="['cursor-pointer', { done: todo.completed }]"
        @click="selectTodo(todo.id)"
        >{{ todo.title }}</span
      >
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
.cursor-pointer {
  cursor: pointer;
}
</style>
