<template>
  <b-container>
    <h1>Instrumentos Médicos</h1>

    <!-- Creacion del formulario -->
    <b-form @submit="createMedicalInstruments" @reset="resetForm">
      <b-form-group
        id="input-group-1"
        label="Nombre del instrumento médico"
        label-for="input-1"
      >
        <b-form-input
          id="input-1"
          v-model="medicalInstrument.instrumentName"
          type="text"
          placeholder="Ingrese nombre del instrumento médico"
          required
        ></b-form-input>
      </b-form-group>

      <b-form-group id="input-group-2" label="Cantidad:" label-for="input-2">
        <b-form-input
          id="input-2"
          v-model="medicalInstrument.quantity"
          type="number"
          placeholder="Ingresar cantidad"
          required
        ></b-form-input>
      </b-form-group>

      <b-form-group id="input-group-3" label="Precio:" label-for="input-3">
        <b-form-input
          id="input-3"
          v-model="medicalInstrument.price"
          type="number"
          placeholder="Ingresar precio"
          required
        ></b-form-input>
      </b-form-group>

      <br />
      <!-- Actions -->
      <b-button type="submit" variant="primary" v-if="!editing"
        >Crear Instrumento médico</b-button
      >
      <b-button variant="success" v-else @click="updateMedicalInstrument"
        >Guardar Instrumento médico</b-button
      >
      <b-button type="reset" variant="danger">Limpiar formulario</b-button>
    </b-form>

    <b-table striped hover :items="medicalInstruments" :fields="headers">
      <template #cell(Opciones)="row">
        <b-button size="sm" @click="loadmedicalInstrument(row)" class="mr-2">
          Modificar Instrumento Médico
        </b-button>
        <b-button size="sm" @click="deletemedicalInstruments(row)" class="mr-2">
          Eliminar Instrumento Médico
        </b-button>
      </template>
    </b-table>

    <alerts :message="message" />
  </b-container>
</template>
<script>
// import alerts from "~/components/alerts.vue";
export default {
  layout:"admin",
  // components: { alerts },
  // Información a utilizar
  data() {
    return {
      headers: ["instrumentName", "quantity", "price", "Opciones"],
      medicalInstruments: [],
      medicalInstrument: {},
      editing: false,
      universidades: ["Universidad de Medellin", "Universidad de Antioquia"],
      message: "",
      opcionesAxios: null,
    };
  },
  // Método antes de que cargue la página
  beforeMount() {
     this.loadHeader();
    this.loadMedicalInstruments();

  },
  // Métodos
  methods: {
    loadHeader() {
      let token = localStorage.getItem("token");
      this.opcionesAxios = { headers: { token } };
    },
    async loadMedicalInstruments() {

      // Cargar los usuarios de la base de datos
      const url = "http://localhost:3001/api/v1/medical_instruments";
      let { data } = await this.$axios.get(url, this.opcionesAxios);
      console.log(data);
      if (data.ok === true) {
        this.medicalInstruments = data.info;
      } else {
        alert("No se cargaron los medicamentos");
      }
    },
    async createMedicalInstruments(event) {
      event.preventDefault();
      const url = "http://localhost:3001/api/v1/medical_instruments";
      let { data } = await this.$axios.post(url, this.medicalInstrument, this.opcionesAxios);
      this.$swal.fire("Creado.", data.message, "success");
      this.loadMedicalInstruments();
    },
    async updateMedicalInstrument(event) {
      event.preventDefault();
      const url = `http://localhost:3001/api/v1/medical_instruments/${this.medicalInstrument._id}`;
      let { data } = await this.$axios.put(url, this.medicalInstrument, this.opcionesAxios);
      console.log(data);
      this.$swal.fire("Actualizado.", data.message, "success");
      this.resetForm();
      this.loadMedicalInstruments();
    },
    loadmedicalInstrument(medicalInstrument) {
      this.editing = true;
      this.medicalInstrument = Object.assign({}, medicalInstrument.item);
    },
    resetForm() {
      this.editing = false;
      this.medicalInstrument = {};
    },
    async deletemedicalInstruments({ item }) {
      this.$swal
        .fire({
          title: "¿Esta seguro de eliminar?",
          text: "No se puede recuperar despúes de la eliminación.",
          type: "warning",
          showCancelButton: true,
          confirmButtonColor: "#3085d6",
          cancelButtonColor: "#d33",
          confirmButtonText: "Si, eliminar!",
          cancelButtonText: "Cancelar",
        })
        .then(async (result) => {
          if (result.value == true) {
            const url = `http://localhost:3001/api/v1/medical_instruments/${item._id}`;
            let { data } = await this.$axios.delete(url, this.opcionesAxios);
            this.loadMedicalInstruments();
            this.$swal.fire("Eliminado!", data.message, "success");
          }
        });
    },
  },
};
</script>
