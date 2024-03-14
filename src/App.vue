<script setup>
import { ref, onMounted, computed, watch } from 'vue'

const list = ref([])
const name = ref('')
let id = 0

const newItem = ref('')
const addType = ref(null)

watch(name, (newVal) => {
  localStorage.setItem('name', newVal)
})

watch(list, (newVal) => {
  localStorage.setItem('list', JSON.stringify(newVal))
}, {
  deep: true
})

const orderList = computed(() => list.value.sort((a, b) => {
  return b.createdAt - a.createdAt
}))

function addItem() {
  if (addType.value.trim() === '' || addType.value.trim() === null) return

  if (newItem.value.trim() === '') return

  list.value.push({ id: id++, text: newItem.value, done: false, type: addType.value, createdAt: new Date().getTime() })
  addType.value = null
  newItem.value = ''
}

function removItem(item) {
  list.value = list.value.filter((t) => t !== item)
}

onMounted(() => {
  name.value = localStorage.getItem('name') || ''
  list.value = JSON.parse(localStorage.getItem('todos')) || []
})
</script>

<template>
  <main class="flex flex-col items-center pt-10 gap-4 bg-slate-200 h-screen">
    <section class="flex text-xl rounded-m font-semibold leading-6">
      <h2 class="text-3xl font-semibold">Tarefas do <input type="text" id="name" placeholder="Digite Seu Nome"
          v-model="name" class="w-auto bg-slate-200 max-w-20" /></h2>
    </section>

    <section class="">
      <form @submit.prevent="addItem" class="flex flex-col gap-6">
        <input class="shadow-md p-2" v-model="newItem" required placeholder="Adicione uma tarefa">
        <div class="flex gap-6 w-full justify-center">
          <label class="flex flex-col items-center bg-white p-4 rounded-lg shadow-md shadow-green-500/50 w-44">
            <input type="radio" name="type" value="personal" v-model="addType" class="form-radio accent-green-600" />
            <div> Pessoal </div>
          </label>
          <label class="flex flex-col items-center bg-white p-4 rounded-lg shadow-md shadow-blue-500/50 w-44">
            <input type="radio" name="type" value="job" v-model="addType" class="form-radio accent-blue-600" />
            <div> Trabalho </div>
          </label>
        </div>
        <button
          class="flex justify-center w-full text-xl rounded-md bg-cyan-600 p-3 font-semibold leading-6 text-white shadow-sm hover:bg-cyan-900 focus:outline-none focus-visible:ring focus-visible:ring-green-500 focus-visible:ring-opacity-50">Adicionar</button>
      </form>
    </section>

    <section>
      <ul>
        <h3 class="w-96 text-xl font-semibold">LISTA:</h3>
        <div v-for="item in orderList"
          class="flex w-full justify-between text-lg font-semibold mt-4 bg-white p-4 rounded-lg shadow-md" :class="{
            'shadow-green-500/50': item.type === 'personal',
            'shadow-blue-500/50': item.type === 'job'
          }">
          <label class="flex gap-2" >
            <input type="checkbox" v-model="item.done" :class="{
            'accent-green-600': item.done && item.type === 'personal',
            'accent-blue-600': item.done && item.type === 'job'
          }" />
        <span :class="{'line-through': item.done}">{{ item.text }}</span>
          </label>
          <button @click=" removItem(item)"
            class="text-xs rounded-md bg-red-500 p-2 font-semibold text-white shadow-sm hover:bg-red-600 focus-visible:ring-red-500 focus-visible:ring-opacity-50">X</button>
        </div>
      </ul>
    </section>
  </main>
</template>