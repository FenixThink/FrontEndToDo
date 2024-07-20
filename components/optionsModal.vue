<template>
  <div>
    <transition name="fade">
        <div v-if="modalVisible" class="fixed inset-0 flex items-center justify-center bg-gray-800 bg-opacity-50">
            <div class="bg-white p-6 rounded-lg shadow-lg max-w-sm w-full">
                <h3 class="text-xl font-semibold mb-4">Opciones</h3>
                <p class="mb-4">Selecciona una acción para el elemento con ID: {{ data.id }}</p>
                <div class="flex justify-end gap-4">
                <button @click="editElement" class="bg-blue-500 text-white px-4 py-2 rounded-lg">Editar</button>
                <button @click="deleteElement" class="bg-red-500 text-white px-4 py-2 rounded-lg">Eliminar</button>
                <button @click="closeModal" class="bg-gray-300 text-gray-800 px-4 py-2 rounded-lg">Cancelar</button>
                </div>
            </div>
        </div>
      </transition>
      <create-form-modal :modalVisible="editFormModal" @closeModal="handleModalCreate" :data="data" @refresh="refresh"/>
    </div>
</template>

<script setup>
import { ref, defineProps } from 'vue'

const editFormModal = ref(false)

const props = defineProps({
    modalVisible: Boolean, // Cambiado a Array
    data: Object
})

const handleModalCreate = () => {
    editFormModal.value = !editFormModal.value
}

const editElement = () => {

  handleModalCreate()
}

const deleteElement = () => {
    const id = props.data.id;
    fetch(`http://localhost:3000/todos/${id}`, {
        method: 'DELETE',
    })
  closeModal()
  refresh()
}

const emit = defineEmits(['customEvent','refresh'])

const refresh = () => {
    emit('refresh')
}

const closeModal = () => {
  emit('customEvent', null)
}

</script>