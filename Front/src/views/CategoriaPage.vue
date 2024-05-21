<template>
  <ion-page>
    <ion-header>
      <ion-toolbar>
        <ion-title>Categoria</ion-title>
      </ion-toolbar>
    </ion-header>
    <ion-content>
      <ion-list>
        <ion-item v-for="categoria in categorias" :key="categoria.id">
          {{ categoria.name }}
          <ion-button @click="editCategoria(categoria)" slot="end">Editar</ion-button>
          <ion-button @click="deleteCategoria(categoria.id)" slot="end" color="danger">Eliminar</ion-button>
        </ion-item>
      </ion-list>
      <ion-button @click="showCreateForm = true">Agregar Categoria</ion-button>
      
      <ion-modal v-if="showCreateForm || showEditForm" @did-dismiss="resetForm">
        <ion-header>
          <ion-toolbar>
            <ion-title>{{ showEditForm ? 'Editar' : 'Agregar' }} Categoria</ion-title>
          </ion-toolbar>
        </ion-header>
        <ion-content>
          <ion-item>
            <ion-label position="floating">Nombre</ion-label>
            <ion-input v-model="categoriaForm.name"></ion-input>
          </ion-item>
          <ion-button @click="showEditForm ? updateCategoria() : createCategoria()">
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
  name: 'CategoriaPage',
  setup() {
    const categorias = ref([]);
    const showCreateForm = ref(false);
    const showEditForm = ref(false);
    const categoriaForm = ref({ id: null, name: '' });

    const fetchCategorias = async () => {
      try {
        const response = await axios.get('http://localhost:9000/api/categorias');
        categorias.value = response.data;
      } catch (error) {
        console.error('Error fetching categorias:', error);
      }
    };

    const createCategoria = async () => {
      try {
        await axios.post('http://localhost:9000/api/categorias', categoriaForm.value);
        fetchCategorias();
        resetForm();
      } catch (error) {
        console.error('Error creating categoria:', error);
      }
    };

    const editCategoria = (categoria) => {
      categoriaForm.value = { ...categoria };
      showEditForm.value = true;
    };

    const updateCategoria = async () => {
      try {
        await axios.put(`http://localhost:9000/api/categorias/${categoriaForm.value.id}`, categoriaForm.value);
        fetchCategorias();
        resetForm();
      } catch (error) {
        console.error('Error updating categoria:', error);
      }
    };

    const deleteCategoria = async (id) => {
      try {
        await axios.delete(`http://localhost:9000/api/categorias/${id}`);
        fetchCategorias();
      } catch (error) {
        console.error('Error deleting categoria:', error);
      }
    };

    const resetForm = () => {
      categoriaForm.value = { id: null, name: '' };
      showCreateForm.value = false;
      showEditForm.value = false;
    };

    onMounted(fetchCategorias);

    return {
      categorias,
      showCreateForm,
      showEditForm,
      categoriaForm,
      createCategoria,
      editCategoria,
      updateCategoria,
      deleteCategoria,
      resetForm
    };
  }
});
</script>

<style scoped>
/* Estilos específicos */
</style>
