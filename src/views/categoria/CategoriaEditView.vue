<template>
  <div>
    <h1>Editar Categoría</h1>
    <form @submit.prevent="submitForm" v-if="form">
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
      <button type="submit" class="btn btn-primary">Guardar Cambios</button>
    </form>
  </div>
</template>
  
<script>
import { mapState, mapGetters, mapActions } from 'vuex'
export default {
  name: 'CategoriaEdit',
  data() {
    return {
      estadoList: [
        "Activo",
        "Inactivo"
      ],
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
      this.axios.patch(this.baseUrl + "/categorias/" + this.item.id, this.form)
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
  },
  computed: {
    // propiedades computadas que dependen de otras propiedades reactivas
    ...mapState(['count']),
    ...mapGetters(['doubleCount', 'getBaseUrl']),
    baseUrl() {
      return this.getBaseUrl
    },
    form() {
      return Object.assign({},this.item);
    }
  },
  mounted() {
    this.getCategoriaList();
  },
  props: ['item']
}
</script>
  
<style scoped></style>
  