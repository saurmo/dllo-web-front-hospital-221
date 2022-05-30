<template>
  <b-container>
    <h1>Nutrition Types</h1>
    <!-- Creacion del formulario -->
    <b-form @submit="createNutritionType" @reset="resetForm">
      <div class="inputs">
        <div class="input-data">
          <b-form-group
            id="input-group-1"
            label="Nutrition code:"
            label-for="input-1"
          >
            <b-form-input
              id="input-1"
              :disabled="editing"
              v-model="nutritionType.nutritionCode"
              placeholder="Enter nutrition code"
            ></b-form-input>
          </b-form-group>
        </div>
        <div class="input-data">
          <b-form-group id="input-group-2" label="Name:" label-for="input-2">
            <b-form-input
              id="input-2"
              :disabled="editing"
              v-model="nutritionType.name"
              placeholder="Enter name"
              type="text"
              pattern="[a-zA-Z ]{2,254}"
            ></b-form-input>
          </b-form-group>
        </div>
        <div class="input-data">
          <b-form-group
            id="input-group-2"
            label="Description:"
            label-for="input-2"
          >
            <b-form-textarea
              id="input-2"
              v-model="nutritionType.description"
              placeholder="Enter description"
            ></b-form-textarea>
          </b-form-group>
        </div>
        <div class="input-data">
          <b-form-group
            id="input-group-2"
            label="Periodicity:"
            label-for="input-2"
          >
            <b-form-input
              id="input-2"
              v-model="nutritionType.periodicity"
              placeholder="Enter periodicity"
            ></b-form-input>
          </b-form-group>
        </div>
      </div>
      <br />
      <!-- Actions -->
      <b-button type="submit" variant="success" v-if="!editing"
        >CREATE</b-button
      >
      <b-button variant="success" v-else @click="updateNutritionType"
        >SAVE</b-button
      >
      <b-button type="reset" variant="danger">CANCEL</b-button>
    </b-form>

    <b-table bordered striped hover :items="nutritionTypes" :fields="headers" class="table-data">
      <template #cell(options)="row">
        <b-button
          size="sm"
          @click="loadNutritionType(row)"
          class="mr-2"
          variant="primary"
        >
          UPDATE
        </b-button>
        <b-button
          size="sm"
          @click="deleteNutritionType(row)"
          class="mr-2"
          variant="danger"
        >
          DELETE
        </b-button>
      </template>
    </b-table>
  </b-container>
</template>
<script>
export default {
  layout: "admin",
  //components: { alerts },
  // Información a utilizar
  data() {
    return {
      headers: [
        "nutritionCode",
        "name",
        "description",
        "periodicity",
        "options",
      ],
      nutritionTypes: [],
      nutritionType: {},
      editing: false,
      message: "",
      opcionesAxios: null,
    };
  },
  // Método antes de que cargue la página
  beforeMount() {
    this.loadHeader();
    this.loadNutritionTypes();
  },
  // Métodos
  methods: {
    loadHeader() {
      let token = localStorage.getItem("token");
      this.opcionesAxios = { headers: { token } };
    },
    async loadNutritionTypes() {
      // Cargar los usuarios de la base de datos
      const url = "http://localhost:3001/api/v1/nutrition-types";
      let { data } = await this.$axios.get(url, this.opcionesAxios);
      console.log(data);
      if (data.ok === true) {
        this.nutritionTypes = data.info;
      } else {
        this.$swal.fire("Error fetching data", data.message, "error");
      }
    },
    async createNutritionType(event) {
      event.preventDefault();
      const url = "http://localhost:3001/api/v1/nutrition-types";
      let { data } = await this.$axios.post(
        url,
        this.nutritionType,
        this.opcionesAxios
      );
      this.message = data.message;
      if (data.ok == true) {
        this.$swal.fire(data.message, data.info, "success");
      } else {
        this.$swal.fire(data.message, data.info, "error");
      }
      this.loadNutritionTypes();
      this.resetForm();
    },
    async updateNutritionType(event) {
      event.preventDefault();
      try {
        this.editing = true;
        const url = `http://localhost:3001/api/v1/nutrition-types/${this.nutritionType.nutritionCode}`;
        const dataToUpdate = {
          nutritionCode: this.nutritionType.nutritionCode,
          name: this.nutritionType.name,
          description: this.nutritionType.description,
          periodicity: this.nutritionType.periodicity,
        };
        let { data } = await this.$axios.put(
          url,
          dataToUpdate,
          this.opcionesAxios
        );
        console.log(data);
        if (data.ok == true) {
          this.$swal.fire(data.message, data.info, "success");
        } else {
          this.$swal.fire(data.message, data.info, "error");
        }
        this.resetForm();
        this.loadNutritionTypes();
      } catch (error) {
        this.$swal.fire(
          "ERROR",
          "An error has ocurred with the server",
          "error"
        );
      }
    },
    loadNutritionType(nutritionType) {
      this.editing = true;
      this.nutritionType = Object.assign({}, nutritionType.item);
    },
    resetForm() {
      this.editing = false;
      this.nutritionType = {};
    },
    async deleteNutritionType({ item }) {
      this.$swal
        .fire({
          title: "¿Are you sure?",
          text: "It can not be possible to recover the data after doing this.",
          type: "warning",
          showCancelButton: true,
          confirmButtonColor: "#3085d6",
          cancelButtonColor: "#d33",
          confirmButtonText: "Yes, delete!",
          cancelButtonText: "Cancel",
        })
        .then(async (result) => {
          if (result.value == true) {
            const url = `http://localhost:3001/api/v1/nutrition-types/${item.nutritionCode}`;
            let { data } = await this.$axios.delete(url, this.opcionesAxios);
            if (data.ok == true) {
              this.$swal.fire(data.message, data.info, "success");
            } else {
              this.$swal.fire(data.message, data.info, "error");
            }
            this.loadNutritionTypes();
            this.$swal.fire("Deleted!", data.message, "success");
          }
        });
    },
  },
};
</script>
<style>

.table-data {
  margin-top: 20px;
  border: solid;
}

.input-data {
  margin-top: 15px;
  margin-left: 20px;
  margin-right: 20px;
  padding-right: 20px;
  padding-left: 100px;
  width: 500px;
}

.inputs {
  display: flex;
  flex-wrap: wrap;
}
</style>
