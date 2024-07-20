<template>
  <div>
    <!-- Componente draggable -->
    <draggable
      class="space-y-10"
      v-model="data.updateValue"
      group="tasks"
      :animation="200"
      :disabled="false"
      item-key="id"
      @end="(event)=>onEnd(event,element)"
    >
      <template #header>
        <h2 class="text-2xl font-bold mb-4 text-gray-800 h-8">{{ titleCard }}</h2>
      </template>
      
      <template #item="{ element }">
        <div class="relative bg-white p-6 rounded-lg shadow-lg hover:shadow-xl transition-shadow duration-300">
          <button @click="openModal(element)" class="absolute top-2 right-2 text-gray-600 hover:text-gray-900">
            <svg class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 12h12m-6-6v12"></path>
            </svg>
          </button>
          <h3 class="text-lg font-semibold mb-3">{{ element?.title }}</h3>
          <p>{{ element?.description }}</p>
        </div>
      </template>
    </draggable>

  </div>
</template>

<script setup>
import { reactive } from 'vue'
import draggable from 'vuedraggable'

// Definir props
const props = defineProps({
  todoitems: Array, // Cambiado a Array
  titleCard: String
})

// Crear estado reactivo
const data = reactive({
  updateValue: props.todoitems
})

// Definir métodos
const onEnd = (event) => {
    const evento = (event.to.firstChild.textContent);
    const id = (event.target.__draggable_component__.context.element.id)
    const title = (event.target.__draggable_component__.context.element.title)
    const description = (event.target.__draggable_component__.context.element.description)
    const status = ref(null);

    if (evento == "Por hacer") {
        status.value = 0; 
    } else if (evento == "En progreso") {
        status.value = 1; 
    } else if (evento == "Finalizado") {
        status.value = 2; 
    }
    const data = {
        title: title,
        description: description,
        status: status.value
    }
    fetch(`http://localhost:3000/todos/${id}`, {
        method: 'PUT',
        headers: {
            'Content-Type': 'application/json'
        },
        body: JSON.stringify(data)
    })
    // refresh()
}

const emit = defineEmits(['customEvent','refresh'])

const refresh = () => {
    emit('refresh')
}

const openModal = (element) => {
  const data = element
  emit('customEvent', data)
}

</script>
