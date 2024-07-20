<template>
    <div class="p-6 bg-gray-100">
      <div v-if="status === 'pending'">
      Loading ...
      </div>
      <div v-else-if="status == 'success'" class="grid grid-cols-1 md:grid-cols-3 gap-12 h-min">

         <dragable-card v-if="todoItems" :todoitems="todoItems" titleCard="Por hacer" @customEvent="handleModalOpen" @refresh="refreshData"/>
          
          <dragable-card :todoitems="inProgressItems" titleCard="En progreso" @customEvent="handleModalOpen" @refresh="refreshData"/>
          
          <dragable-card :todoitems="completedItems" titleCard="Finalizado" @customEvent="handleModalOpen"  @refresh="refreshData"/>
          
          <!-- Modals -->

          <optionsModal :modalVisible="modalVisible" :data="id" @customEvent="handleModalOpen" @refresh="refreshData" />

          <createFormModal :modalVisible="modalCreateVisible" @closeModal="handleModalCreate" @refresh="refreshData"/>

          <button @click="handleModalCreate(null)" class="bg-blue-500 text-white px-2 py-2 rounded-lg">Crear</button>
      </div>
      
    </div>
</template>
  
  
<script setup>
import { ref } from 'vue'
import { useRouter } from 'vue-router'

const router = useRouter()

const { data, status } = await useAsyncData(
  () => $fetch('http://localhost:3000/todos')
)

const refreshData = async () => {
  router.go('/')
}

const statuses = { 0: [], 1: [], 2: [] };
const todoItems = ref(statuses[0])
const inProgressItems = ref(statuses[1])
const completedItems = ref(statuses[2])

const modalVisible = ref(false)
const modalCreateVisible = ref(false)
const id = ref(null)

if (data ) {
    
const items = data?.value;

// Utiliza reduce para clasificar los objetos por su status
items?.forEach(item => {
  if (statuses[item.status]) {
    statuses[item.status].push(item);
  }
});

}

// Datos iniciales para las columnas
const handleModalCreate = () => {
  modalCreateVisible.value = !modalCreateVisible.value
}
const handleModalOpen = (idOpen) => {
  modalVisible.value = !modalVisible.value 
  id.value = idOpen
}

</script>

<style scoped>
/* Estilos para el modal */
.fade-enter-active, .fade-leave-active {
  transition: opacity 0.5s
}
.fade-enter, .fade-leave-to /* .fade-leave-active in <2.1.8 */ {
  opacity: 0
}
</style>