<script setup>

import { ref, defineComponent } from 'vue'
import Vue3TagsInput from 'vue3-tags-input'

defineComponent({
    components: {
        Vue3TagsInput
    }
})

const tags = ['todo', 'daily']

const newTodo = ref('');
const newTodoDesc = ref('');
const emit = defineEmits(['addTodo']);
const isShowDesc = ref(false)

function addTodo(event){
    event.preventDefault();

    fetch('http://localhost:8000/api/todos', {
        method: 'POST',
        headers: {
            'Content-Type': 'application/json'
        },
        body: JSON.stringify({
            title: newTodo.value,
            status: false,
            description: newTodoDesc.value,
            tags: ['todo', 'daily']
        })

    })
    .then(response => response.json())
    .then(json => {
        console.log(json)
        emit('addTodo', json)
        newTodo.value = ''
        newTodoDesc.value = ''

    })
}

function toggleShowDesc(){
    isShowDesc.value = !isShowDesc.value
}

</script>

<template>
    <form @submit.prevent="addTodo">
      <input type="text" v-model="newTodo" @keyup.enter="addTodo" @focus="isShowDesc = true" :class="[ 'w-full border border-slate-300 p-2 mt-4 focus:outline-none', isShowDesc ? 'border-b-none border-b-0 rounded-t-md' : 'rounded-md' ]" :placeholder="isShowDesc ? 'Judul' : 'Tugas baru'" />
      <input v-if="isShowDesc" @keyup.enter="addTodo" ref="inputtitle" @focus="isShowDesc = true" type="text" v-model="newTodoDesc" class="w-full border border-top focus:outline-none border-slate-300 rounded-t-none  rounded-b-none p-2 " placeholder="Deskripsi" />
      <vue3-tags-input v-if="isShowDesc" :tags="tags" @keyup.enter="addTodo" @focus="isShowDesc = true" type="text" class="w-full border border-top focus:outline-none border-slate-300 rounded-t-none rounded-b-md p-2  border-t-0" placeholder="Tags" />
      <input v-if="isShowDesc" type="button" @click="addTodo" class="w-full text-center p-2 mt-2 bg-green-600 text-white rounded-md hover:bg-green-400 cursor-pointer" value="Simpan" />
      <input v-if="isShowDesc" type="button" @click="isShowDesc = false"  class="w-full text-center p-2 mt-2 cursor-pointer hover:underline hover:text-red-800  text-sm" value="Tutup" />
    </form>
</template>

<style lang="css">
.v3ti {
    @apply w-full border border-t-0 border-slate-300 p-1 focus:outline-none  focus:ring-0 ;
}

.v3ti--focus {
    @apply border ring-0;
}

.v3ti input.v3ti-new-tag {
    @apply p-0 m-0;
}

</style>