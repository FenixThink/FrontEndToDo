<template>
    <transition name="fade">
      <div v-if="modalVisible" class="fixed inset-0 flex items-center justify-center bg-gray-800 bg-opacity-50">
        <div class="bg-white p-6 rounded-lg shadow-lg max-w-lg w-full">
          <h2 v-if="data?.title" class="text-2xl font-bold mb-4">Actualizar Tarea</h2>
          <h2 v-else class="text-2xl font-bold mb-4">Crear Tarea</h2>
          <form @submit.prevent="submitForm" class="space-y-4">
            <div>
              <label for="title" class="block text-sm font-medium text-gray-700">Título</label>
              <input
                v-model="title"
                type="text"
                id="title"
                class="mt-1 block w-full p-2 border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-indigo-500 focus:border-indigo-500"
                required
              />
            </div>
            <div>
              <label for="description" class="block text-sm font-medium text-gray-700">Descripción</label>
              <textarea
                v-model="description"
                id="description"
                rows="4"
                class="mt-1 block w-full p-2 border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-indigo-500 focus:border-indigo-500"
                required
              ></textarea>
            </div>
            <div class="flex justify-end space-x-2">
              <button
                type="button"
                @click="closeModal"
                class="bg-gray-300 text-gray-800 px-4 py-2 rounded-md hover:bg-gray-400"
              >
                Cancelar
              </button>
              <button
                v-if="data?.title"
                type="button"
                class="bg-blue-500 text-white px-4 py-2 rounded-md hover:bg-blue-700"
                @click="updateForm"
              >
                Actualizar
              </button>
              <button
                v-else
                type="submit"
                class="bg-blue-500 text-white px-4 py-2 rounded-md hover:bg-blue-700"
              >
                Crear
              </button>
            </div>
          </form>
        </div>
      </div>
    </transition>
  </template>
  
  <script setup>
  import { ref, watch } from 'vue'
  import { defineProps, defineEmits } from 'vue'
  
  const props = defineProps({
    modalVisible: Boolean,
    data: Object
  })
  
  const title = ref('')
  const description = ref('')
  
  watch(
    () => props.data,
    (newData) => {
      title.value = newData?.title || ''
      description.value = newData?.description || ''
    },
    { immediate: true }
  )
  
  const updateForm = async () => {
    const id = props.data.id
    await fetch(`http://localhost:3000/todos/${id}`, {
      method: 'PUT',
      headers: {
        'Content-Type': 'application/json'
      },
      body: JSON.stringify({
        title: title.value,
        description: description.value
      })
    })
    refresh()
    closeModal()
  }
  
  const submitForm = async () => {
    const newItem = {
      title: title.value,
      description: description.value,
      status: 0
    }
  
    await fetch('http://localhost:3000/todos', {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json'
      },
      body: JSON.stringify(newItem)
    })
  
    // Reiniciar el formulario
    title.value = ''
    description.value = ''
    refresh()
  }
  
  const emit = defineEmits(['closeModal', 'refresh'])
  const refresh = () => {
    emit('refresh')
  }
  
  const closeModal = () => {
    emit('closeModal')
  }
  </script>
  
  <style scoped>
  .fade-enter-active, .fade-leave-active {
    transition: opacity 0.5s;
  }
  .fade-enter, .fade-leave-to /* .fade-leave-active in <2.1.8 */ {
    opacity: 0;
  }
  </style>
  