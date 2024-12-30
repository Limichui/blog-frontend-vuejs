<template>
    <div>
        <Modal v-model:modelValue="showModalNuevo">
            <DocenteNewView @on-register="onRegister($event)" />
        </Modal>
        <Modal v-model:modelValue="showModalEdit">
            <DocenteEditView @on-update="onUpdate($event)" :item="itemToEdit" />
        </Modal>

        <!-- Modal de Confirmación -->
        <ConfirmModal
        v-if="showConfirmModal"
        :visible="showConfirmModal"
        title="Eliminar Docente"
        message="¿Estás seguro de que deseas eliminar este registro?"
        @confirm="confirmDelete"
        @cancel="cancelDelete"
        />
        <h1>Lista de Docentes</h1>
        <button @click="showModalNuevo = true" class="btn btn-primary">Nuevo</button>
        <input type="search" style="float:right" v-model="textToSearch" @search="buscar()" placeholder="Buscar por nombre">
        <table>
            <thead>
                <tr>
                    <th>No.</th>
                    <th>Nombre</th>
                    <th>Email</th>
                    <th>Dirección</th>
                    <th>Teléfono</th>
                    <th>Acciones</th>
                </tr>
            </thead>
            <tbody>
                <tr v-for="(item, index) in filteredItems" :key="index">
                    <td>{{ 1 + index }}</td>
                    <td>{{ item.nombre }}</td>
                    <td>{{ item.email }}</td>
                    <td>{{ item.direccion }}</td>
                    <td>{{ item.telefono }}</td>
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
import DocenteNewView from './DocenteNewView.vue'
import DocenteEditView from './DocenteEditView.vue'
import ConfirmModal from '@/components/ConfirmModal.vue'

export default {
    name: 'Docente',
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
            itemList: []
        }
    },
    components: {
        // Registro de componentes que se utilizaran.
        Modal,
        DocenteNewView,
        DocenteEditView,
        ConfirmModal
    },
    methods: {
        // métodos que se pueden llamar desde la plantilla o desde otras partes del componente.
        ...mapActions(['increment']),
        getList() {
            const vm = this;
            this.axios.get(this.baseUrl + "/docentes?&q=" + this.textToSearch)
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
            this.axios.delete(this.baseUrl + "/docentes/" + id)
                .then(function (response) {
                    //console.log(response);
                    vm.getList();
                    vm.$toast.show("Registro eliminado.", "danger");
                })
                .catch(function (error) {
                    console.error(error);
                });

        },
        buscar() {
            this.getList();
        },
        onRegister(event) {
            console.log("on register");
            this.getList();
            this.showModalNuevo = false;
            this.$toast.show('Registro exitoso', 'success');
        },
        onUpdate(event) {
            console.log("on update");
            this.getList();
            this.showModalEdit = false;
            this.itemToEdit = null;
            this.$toast.show('Edicion exitosa', 'info');
        },
        showToast(message, type) {
            console.log("showToast");
            this.$toast.show(message, type);
        }
    },
    computed: {
        // propiedades computadas que dependen de otras propiedades reactivas
        ...mapState(['count']),
        ...mapGetters(['doubleCount', 'getBaseUrl']),
        baseUrl() {
            return this.getBaseUrl
        },
        filteredItems() {
            if (!this.textToSearch) {
            return this.itemList; // Si no hay texto, muestra todos los elementos
            }

            // Filtrar elementos por coincidencia parcial en el nombre (ignorar mayúsculas/minúsculas)
            const searchText = this.textToSearch.toLowerCase();
            return this.itemList.filter(item => item.nombre.toLowerCase().includes(searchText));
        }
    },
    props: {
        // propiedades que el componente puede recibir.
    },
    mounted() {
        this.getList();
    },
    emits: [] // los eventos personalizados que el componente puede emitir.
}
</script>

<style></style>