<template>
  <div>
    <h1>Registrar Categoría</h1>
    <form @submit.prevent="submitForm">
      <div class="form-group">
        <label for="name">Nombre de la categoría:</label>
        <input type="text" id="name" v-model="form.nombre" :class="{ 'is-invalid': errors.nombre }"
          placeholder="Ingrese el nombre" />
        <div v-if="errors.nombre" class="invalid-feedback">{{ errors.nombre }}</div>
      </div>

      <div class="form-group">
        <label for="estado">Estado:</label>
        <select id="estado" v-model="form.estado" :class="{ 'is-invalid': errors.estado }">
          <option :value="estado" v-for="(estado, index) in estadoList" :key="`estado-${index}`">{{ estado }}</option>
        </select>
        <div v-if="errors.estado" class="invalid-feedback">{{ errors.estado }}</div>
      </div>
      <button type="submit" class="btn btn-primary">Registrar</button>
    </form>
  </div>
</template>
  
<script>
import { mapState, mapGetters, mapActions } from 'vuex'
export default {
  name: 'CategoriaNew',
  data() {
    return {
      estadoList: [
        "Activo",
        "Inactivo"
      ],
      form: {
        nombre: '',
        estado: ''
      },
      errors: {}
    };
  },
  methods: {
    ...mapActions(['increment']),
    validateForm() {
      this.errors = {};

      if (!this.form.nombre) {
        this.errors.nombre = 'El nombre es obligatorio.';
      }

      if (!this.form.estado) {
        this.errors.estado= 'El estado es obligatoria.';
      }

      return Object.keys(this.errors).length === 0;
    },

    submitForm() {
      if (this.validateForm()) {
        // Enviar los datos al servidor
        this.save();
        // Reiniciar el formulario
        this.form = {
          nombre: '',
          estado: '',
        };
      }
    },
    save() {
      const vm = this;
      this.axios.post(this.baseUrl + "/categorias", this.form)
        .then(function (response) {
          if (response.status == '201') {
            vm.$emit('on-register', response.data);
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
  },
  computed: {
    // propiedades computadas que dependen de otras propiedades reactivas
    ...mapState(['count']),
    ...mapGetters(['doubleCount', 'getBaseUrl']),
    baseUrl() {
      return this.getBaseUrl
    }
  },
  mounted() {
    this.getCategoriaList();
  },
}
</script>
  
<style scoped></style>
  