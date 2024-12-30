<template>
    <div>
        <Modal v-model:modelValue="showModalNuevo">
            <CursoNewView @on-register="onRegister()" />
        </Modal>
        <Modal v-model:modelValue="showModalEdit">
            <CursoEditView @on-update="onUpdate()" :item="itemToEdit" />
        </Modal>

        <!-- Modal de Confirmación -->
        <ConfirmModal
        v-if="showConfirmModal"
        :visible="showConfirmModal"
        title="Eliminar Curso"
        message="¿Estás seguro de que deseas eliminar este registro?"
        @confirm="confirmDelete"
        @cancel="cancelDelete"
        />

        <h1>Lista de Cursos</h1>
        <button @click="showModalNuevo = true" class="btn btn-primary">Nuevo</button>
        <button @click="buscar()" class="ms-2 btn btn-primary" style="float:right" >Buscar</button>
        <input type="search" style="float:right" v-model="textToSearch" @search="buscar()" placeholder="Buscar por título">
        <div style="margin: 20px 0;">
            <h3>Filtros:</h3>
            <form @submit.prevent="filtrar()">

                <label for="cbCategoria"> Categoría: </label>
                <select class="ms-1 me-3" id="cbCategoria" v-model="filter.categoriaId">
                    <option value="">Todos</option>
                    <option :value="categoria.id" v-for="(categoria, index) in categoriaList" :key="`categoria-${index}`">{{ categoria.nombre }}
                    </option>
                </select>

                <label for="cbDocente"> Docente: </label>
                <select class="ms-1 me-3" id="cbDocente" v-model="filter.veterinarioId">
                    <option value="">Todos</option>
                    <option :value="veterinario.id" v-for="(veterinario, index) in veterinarioList" :key="`veterinario-${index}`">{{ veterinario.nombre }}
                    </option>
                </select>
                <button type="submit" class="btn btn-lith">Fitrar</button>
            </form>
        </div>
        <table>
            <thead>
                <tr>
                    <th>No.</th>
                    <th>Título</th>
                    <th>Duración</th>
                    <th>Categoría</th>
                    <th>Docente</th>
                    <th>Estado</th>
                    <th>Contenido</th>
                    <th>Acciones</th>
                </tr>
            </thead>
            <tbody>
                <tr v-for="(item, index) in itemList" :key="index">
                    <td>{{ 1 + index }}</td>
                    <td>{{ item.titulo }}</td>
                    <td>{{ item.duracion }}</td>
                    <td>{{ item.categoria.nombre }}</td>
                    <td>{{ item.docente.nombre }}</td>
                    <td>{{ item.estado }}</td>
                    <td>{{ item.contenido }}</td>
                    <td>
                        <button @click="edit(item)" class="btn btn-dark" style="margin-right: 15px;">Editar</button>
                        <button @click="promptDelete(item.id)" class="btn btn-danger">Eliminar</button>
                    </td>
                </tr>
            </tbody>
        </table>
    </div>
</template>

<script>
import { mapState, mapGetters, mapActions } from 'vuex'
import Modal from '../../components/Modal.vue'
import CursoNewView from './CursoNewView.vue'
import CursoEditView from './CursoEditView.vue'
import ConfirmModal from '@/components/ConfirmModal.vue'

export default {
    name: 'Curso',
    data() {
        return {
            showConfirmModal: false, // Controla la visibilidad del modal de confirmación
            idToDelete: null,        // Almacena el ID del registro a eliminar
            currentPage: 1,
            totalPages: 100, // Este valor debe ser calculado según tus datos
            showModalNuevo: false,
            showModalEdit: false,
            itemToEdit: null,
            textToSearch: '',
            textToFilter: '',
            itemList: [],
            veterinarioList: [],
            path: '',
            filter: {
                fecha: null,
                veterinarioId:''
            }
        }
    },
    components: {
        // Registro de componentes que se utilizaran.
        Modal,
        CursoNewView,
        CursoEditView,
        ConfirmModal
    },
    methods: {
        // métodos que se pueden llamar desde la plantilla o desde otras partes del componente.

        
        
        ...mapActions(['increment']),
        getList() {
            const vm = this;
            
            this.path = this.baseUrl + "/cursos?_expand=categoria&_expand=docente&q=" + this.textToSearch;

            this.axios.get(this.path)
                .then(function (response) {
                    vm.itemList = response.data;
                })
                .catch(function (error) {
                    console.error(error);
                });
        },
        
        edit(item) {
            this.itemToEdit = Object.assign({}, item);
            this.showModalEdit = true;
        },
        promptDelete(id) {
            this.idToDelete = id;
            this.showConfirmModal = true; // Muestra el modal de confirmación
        },
        confirmDelete() {
            this.eliminar(this.idToDelete);
            this.showConfirmModal = false; // Oculta el modal
            this.idToDelete = null; // Limpia el ID
        },
        cancelDelete() {
            this.showConfirmModal = false; // Oculta el modal
            this.idToDelete = null; // Limpia el ID
        },
        eliminar(id) {
            const vm = this;
            this.axios.delete(this.baseUrl + "/cursos/" + id)
                .then(function (response) {
                    vm.getList();
                    vm.$toast.show("Registro eliminado.", "danger");
                })
                .catch(function (error) {
                    console.error(error);
                });
        },
        buscar(value) {
            this.getList();
        },
        filtrar() {
            this.textToFilter = '';
            if (this.filter.fecha != null && this.filter.fecha != '') {
                this.textToFilter += "&fecha=" + this.filter.fecha;
            }
            if (this.filter.veterinarioId != null && this.filter.veterinarioId != '') {
                this.textToFilter += "&veterinarioId=" + this.filter.veterinarioId;
            }
            this.getList();
        },
        onRegister(event) {
            this.getList();
            this.showModalNuevo = false;
            this.$toast.show('Registro exitoso', 'success');
        },
        onUpdate(event) {
            this.getList();
            this.showModalEdit = false;
            this.itemToEdit = null;
            this.$toast.show('Edicion exitosa', 'info');
        },
        showToast(message, type) {
            this.$toast.show(message, type);
        }
    },
    computed: {
        // propiedades computadas que dependen de otras propiedades reactivas
        ...mapState(['count']),
        ...mapGetters(['doubleCount', 'getBaseUrl']),
        baseUrl() {
            return this.getBaseUrl
        }
    },
    props: {
        // propiedades que el componente puede recibir.
    },
    mounted() {
        this.getList();
        //this.getVeterinarioList();
    },
    emits: [] // los eventos personalizados que el componente puede emitir.
}
</script>

<style></style>