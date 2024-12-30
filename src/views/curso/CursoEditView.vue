<template>
  <div>
    <h1>Editar Curso</h1>
    <form @submit.prevent="submitForm" v-if="form">

      <div class="form-group">
        <label for="titulo">Titulo del curso:</label>
        <input type="text" id="titulo" v-model="form.titulo" :class="{ 'is-invalid': errors.titulo }"
          placeholder="Ingrese el título del curso" />
        <div v-if="errors.titulo" class="invalid-feedback">{{ errors.titulo }}</div>
      </div>

      <div class="form-group">
        <label for="duracion">Duracion: (En minutos)</label>
        <input type="text" id="duracion" v-model="form.duracion" :class="{ 'is-invalid': errors.duracion }"
          placeholder="Ingrese la ducacion del curso" />
        <div v-if="errors.duracion" class="invalid-feedback">{{ errors.duracion }}</div>
      </div>

      <div class="form-group">
        <label for="categoria">Categoría:</label>
        <select id="categoria" v-model="form.categoriaId" :class="{ 'is-invalid': errors.categoriaId }">
          <option :value="categoria.id" v-for="(categoria, index) in categoriaList" :key="`categoria-${index}`">{{ categoria.nombre }}
          </option>
        </select>
        <div v-if="errors.categoriaId" class="invalid-feedback">{{ errors.categoriaId }}</div>
      </div>
      
      <div class="form-group">
        <label for="docente">Docente</label>
        <select id="docente" v-model="form.docenteId" :class="{ 'is-invalid': errors.docenteId }">
          <option :value="docente.id" v-for="(docente, index) in docenteList" :key="`docente-${index}`">{{ docente.nombre }}
          </option>
        </select>
        <div v-if="errors.docenteId" class="invalid-feedback">{{ errors.docenteId }}</div>
      </div>

      <div class="form-group">
        <label for="estado">Estado:</label>
        <select id="estado" v-model="form.estado" :class="{ 'is-invalid': errors.estado }">
          <option :value="estado" v-for="(estado, index) in estadoList" :key="`estado-${index}`">{{ estado }}</option>
        </select>
        <div v-if="errors.estado" class="invalid-feedback">{{ errors.estado }}</div>
      </div>

      <div class="form-group">
        <label for="contenido">Contenido:</label>
        <textarea id="contenido" v-model="form.contenido" :class="{ 'is-invalid': errors.contenido }"
          placeholder="Ingrese un resúmen del curso"></textarea>
        <div v-if="errors.contenido" class="invalid-feedback">{{ errors.contenido }}</div>
      </div>
      
      <button type="submit" class="btn btn-primary">Guardar Cambios</button>
    </form>
  </div>
</template>
  
<script>
import { mapState, mapGetters, mapActions } from 'vuex'
export default {
  name: 'CursoEdit',
  data() {
    return {
      estadoList: [
        "Activo",
        "Inactivo"
      ],
      categoriaList: [],
      docenteList: [],
      errors: {}
    };
  },
  props:['item'],
  methods: {
    ...mapActions(['increment']),
    validateForm() {
      this.errors = {};

      if (!this.form.titulo) {
        this.errors.titulo = 'El Título es obligatorio.';
      }

      if (!this.form.duracion) {
        this.errors.duracion= 'La Duración es obligatorio.';
      }

      if (!this.form.categoriaId) {
        this.errors.categoriaId = 'La Categoría es obligatorio.';
      }
      
      if (!this.form.docenteId) {
        this.errors.docenteId = 'El Docente es obligatorio.';
      }

      if (!this.form.estado) {
        this.errors.estado = 'El Estado es obligatorio.';
      }

      if (!this.form.contenido) {
        this.errors.contenido = 'El Contenido es obligatorio.';
      }


      return Object.keys(this.errors).length === 0;
    },

    submitForm() {
      if (this.validateForm()) {
        // Enviar los datos al servidor
        this.save();
        // Reiniciar el formulario
        this.form = {
          titulo: '',
          duracion:null,
          categoriaId: null,
          docenteId: null,
          estado:'',
          contenido:'',
        };
      }
    },
    save() {
      const vm = this;
      this.axios.patch(this.baseUrl + "/cursos/" + this.item.id, this.form)
        .then(function (response) {
          if (response.status == '200') {
            vm.$emit('on-update', response.data);
          }
          vm.itemList = response.data;
        })
        .catch(function (error) {
          console.error(error);
        });
    },
    
    getCategoriaList() {
      const vm = this;
      this.axios.get(this.baseUrl + "/categorias")
        .then(function (response) {
          vm.categoriaList = response.data;
        })
        .catch(function (error) {
          console.error(error);
        });
    },

    getDocenteList() {
      const vm = this;
      this.axios.get(this.baseUrl + "/docentes")
        .then(function (response) {
          vm.docenteList = response.data;
        })
        .catch(function (error) {
          console.error(error);
        });
    },
  },

  computed: {
    // propiedades computadas que dependen de otras propiedades reactivas
    ...mapState(['count']),
    ...mapGetters(['doubleCount', 'getBaseUrl']),
    baseUrl() {
      return this.getBaseUrl
    },
    form(){
      return Object.assign({},this.item);
    }
  },
  mounted() {
    this.getCategoriaList();
    this.getDocenteList();
  },
}
</script>
  
<style scoped></style>
  