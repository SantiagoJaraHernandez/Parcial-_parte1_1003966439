<template>
  <ion-page>
    <ion-header>
      <ion-toolbar>
        <ion-title>Carro</ion-title>
      </ion-toolbar>
    </ion-header>
    <ion-content>
      <ion-list>
        <ion-item v-for="carro in carros" :key="carro.id">
          {{ carro.name }}
          <ion-button @click="editCarro(carro)" slot="end">Editar</ion-button>
          <ion-button @click="deleteCarro(carro.id)" slot="end" color="danger">Eliminar</ion-button>
        </ion-item>
      </ion-list>
      <ion-button @click="showCreateForm = true">Agregar Carro</ion-button>
      
      <ion-modal v-if="showCreateForm || showEditForm" @did-dismiss="resetForm">
        <ion-header>
          <ion-toolbar>
            <ion-title>{{ showEditForm ? 'Editar' : 'Agregar' }} Carro</ion-title>
          </ion-toolbar>
        </ion-header>
        <ion-content>
          <ion-item>
            <ion-label position="floating">Nombre</ion-label>
            <ion-input v-model="carroForm.name"></ion-input>
          </ion-item>
          <ion-button @click="showEditForm ? updateCarro() : createCarro()">
            {{ showEditForm ? 'Actualizar' : 'Agregar' }}
          </ion-button>
        </ion-content>
      </ion-modal>
    </ion-content>
  </ion-page>
</template>

<script>
import { defineComponent, ref, onMounted } from 'vue';
import axios from 'axios';

export default defineComponent({
  name: 'CarroPage',
  setup() {
    const carros = ref([]);
    const showCreateForm = ref(false);
    const showEditForm = ref(false);
    const carroForm = ref({ id: null, name: '' });

    const fetchCarros = async () => {
      try {
        const response = await axios.get('http://localhost:9000/api/carro');
        carros.value = response.data;
      } catch (error) {
        console.error('Error fetching carros:', error);
      }
    };

    const createCarro = async () => {
      try {
        await axios.post('http://localhost:9000/api/carro', carroForm.value);
        fetchCarros();
        resetForm();
      } catch (error) {
        console.error('Error creating carro:', error);
      }
    };

    const editCarro = (carro) => {
      carroForm.value = { ...carro };
      showEditForm.value = true;
    };

    const updateCarro = async () => {
      try {
        await axios.put(`http://localhost:9000/api/carro/${carroForm.value.id}`, carroForm.value);
        fetchCarros();
        resetForm();
      } catch (error) {
        console.error('Error updating carro:', error);
      }
    };

    const deleteCarro = async (id) => {
      try {
        await axios.delete(`http://localhost:9000/api/carro/${id}`);
        fetchCarros();
      } catch (error) {
        console.error('Error deleting carro:', error);
      }
    };

    const resetForm = () => {
      carroForm.value = { id: null, name: '' };
      showCreateForm.value = false;
      showEditForm.value = false;
    };

    onMounted(fetchCarros);

    return {
      carros,
      showCreateForm,
      showEditForm,
      carroForm,
      createCarro,
      editCarro,
      updateCarro,
      deleteCarro,
      resetForm
    };
  }
});
</script>

<style scoped>
/* Estilos específicos */
</style>
