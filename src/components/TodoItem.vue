<script setup>
import { ref } from 'vue'

const props = defineProps(['todo']);
const todo = ref(props.todo);
const emit = defineEmits(['todoDeleted']);
const editMode = ref(false);
const editData = ref({})

function toggleCompleted(id) {
    fetch(`http://localhost:8000/api/todos/${id}/status`, {
        method: 'PATCH',
        headers: {
            'Content-Type': 'application/json'
        },
    })
    .then(response => response.json())
    .then(json => {
        todo.status = json.status
        emit('todoCompleted', id)
    })
}

function deleteTodo(id){
    fetch(`http://localhost:8000/api/todos/${id}`, {
        method: 'DELETE',
        headers: {
            'Content-Type': 'application/json'
        },
    })
    .then(response => response.status)
    .then(response => {
        if(response == 204){
            emit('todoDeleted', id)
        }
    })
}

function toggleEdit(){
    editMode.value = !editMode.value
}

function updateTodo(event){
    if (event.key === 'Enter') {
        toggleEdit()
    }

    fetch(`http://localhost:8000/api/todos/${todo.value.id}`, {
        method: 'PATCH',
        headers: {
            'Content-Type': 'application/json'
        },
        body: JSON.stringify({
            title: todo.value.title,
            description: todo.value.description
        })
    })
    .then(response => response.json())
    .then(json => {
        // do nothing
    })
}

</script>

<template>
    <div>
        <div v-if="editMode" class="border border-slate-400 rounded-md my-3">
            <input @keyup="updateTodo" type="text" v-model="todo.title" class="font-bold w-full rounded-md p-2 active:outline-none focus:outline-none" placeholder="Title" />
            <input @keyup="updateTodo" type="text" v-model="todo.description" class="w-full rounded-md p-2 active:outline-none focus:outline-none" placeholder="Description" />
            <div class="flex flex-row-reverse">
               <button class="text-sm text-green-900 hover:text-green-700 mx-2 ml-auto hover:underline font-bold mb-2" @click="toggleEdit">Oke</button>
            </div> 
        </div>

        <div v-else class="flex flex-row group/edit hover:bg-gradient-to-r hover:from-gray-50 hover:to-gray-100 py-1 px-2 rounded-md">
            <input type="checkbox" :id="todo.id" :checked="todo.status == '1' ? true : false" @change="toggleCompleted(todo.id)" class="mr-1" />
            <label :for="todo.id">{{ todo.title }}</label>
            <div class="invisible group-hover/edit:visible flexflex-row ml-auto">
                <button class="hover:underline mx-1 text-xs" @click="toggleEdit()">Lihat</button>
                <button class="hover:underline text-red-900 hover:text-red-700 mx-1 text-xs" @click="deleteTodo(todo.id)">Archive</button>
            </div>
        </div>
    </div>
</template>