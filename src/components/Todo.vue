<script setup>
const props = defineProps({
  todo: Object
})
const emit = defineEmits(['selected', 'deleted'])
</script>

<template>
  <input type="checkbox" v-model="todo.completed" />
  <span
    :class="['cursor-pointer', { done: todo.completed }]"
    @click="() => emit('selected', todo.id)"
  >{{ todo.title }}</span>
  <button @click="() => emit('deleted', todo)">X</button>
</template>

<style>
.done {
  text-decoration: line-through;
}
.cursor-pointer {
  cursor: pointer;
}
</style>
