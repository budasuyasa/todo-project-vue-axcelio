<script setup>
import TodoItem from './components/TodoItem.vue'
import AddTodo from './components/AddTodo.vue'
import Search from './components/Search.vue'

import { onMounted, ref } from 'vue'
let todos = ref([]);

defineEmits(['newTodoAdded'])


onMounted(() => {
  fetch('http://localhost:8000/api/todos')
    .then(response => response.json())
    .then(json => todos.value = json)
})

function newTodoAdded(todo){
  todos.value.push(todo)
  console.log(todos.value)
}

function todoFiltered(_todos){
  todos.value = _todos
}

function todoDeleted(id){
  todos.value = todos.value.filter(todo => todo.id != id)
}

</script>

<template>
  <div class="container mx-auto border  border-slate-300 shadow-lg rounded-md p-4 w-4/12 mt-16 flex flex-col">

    <Search @todo-filtered="todoFiltered" />

    <div v-if="todos.length == 0" class="border bg-slate-200 text-black p-2 rounded-md">
      Belum ada tugas. Yuk buat tugas baru!
    </div>

    <div v-for="todo in todos">
      <TodoItem :todo="todo" :key="todo.id" @todo-deleted="todoDeleted" />
    </div>

    <AddTodo @add-todo="newTodoAdded"  />

  </div>
</template>