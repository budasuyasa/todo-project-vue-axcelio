<script setup>
import { ref } from 'vue'
const emit = defineEmits(['todoFiltered']);
const search = ref('')

function filter(){
    fetch(`http://localhost:8000/api/todos?search=${search.value}`)
    .then(response => response.json())
    .then(json => {
        emit('todoFiltered', json)
    })
}

</script>
<template>
    <input type="text" @keyup="filter" v-model="search" class="w-5/12 ml-auto  border border-slate-300 rounded-md p-2 mb-4" placeholder="Cari tugas" />
</template>