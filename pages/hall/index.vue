<template>
  <b-container>
    <h1>Halls</h1>

    <b-form @submit="createhall" @reset="resetForm">
      <b-form-group id="igId" label="Hall ID" label-for="ilIdHall">
        <b-form-input id="iID" v-model="hall.idHall" type="text" placeholder="Hall's Id" required> </b-form-input>
      </b-form-group>

      <b-form-group id="igpiso" label="Piso" label-for="iPiso">
        <b-form-input id="ipiso" v-model="hall.Piso" type="number" placeholder="Number of hall" required> </b-form-input>
      </b-form-group>

      <b-form-group id="igHalltype" label="Halltype" label-for="iHalltype">
        <b-form-input id="iHalltype" v-model="hall.Halltype" type="text" placeholder="Halltype" required> </b-form-input>
      </b-form-group>

      <b-form-group id="igblock" label="block" label-for="igblock">
        <b-form-input id="block" v-model="hall.block" type="number" placeholder="block" required> </b-form-input>
      </b-form-group>

      <br />
     

      <b-button type="submit" variant="primary" v-if="!editing"
        >Create hall</b-button
      >
      <b-button variant="success" v-else @click="updatehall"
        >Save</b-button
      >
      <b-button type="reset" variant="danger">Clear all</b-button>
      </b-form>

    <b-table striped hover :items="halls" :fields="headers">
      <template #cell(Opciones)="row">
        <b-button size="sm" @click="gethalls(row)" class="mr-2">
          Update hall
        </b-button>
        <b-button size="sm" @click="deletehall(row)" class="mr-2">
          Delete hall
        </b-button>
      </template>
    </b-table>

    <alerts :message="message" />
  </b-container>
</template>
<script>


export default {
  layout:"admin",

  data() {
    return {
      headers: ["idHall", "Piso", "Halltype","block", "Opciones"],
      halls: [],
      hall: {},
      editing: false,
      //universidades: ["Universidad de Medellin", "Universidad de Antioquia"],
      //message: "",
      opcionesAxios: null,
    };
  },
  beforeMount() {
     this.loadHeader();
    this.gethalls();
  },
  
  methods: {
    loadHeader() {
      let token = localStorage.getItem("token");
      this.opcionesAxios = { headers: { token } };
    },
    async gethalls() {
      const url = "http://localhost:3001/api/v1/Halls";
      let { data } = await this.$axios.get(url, this.opcionesAxios);
      console.log(data);
      if (data.ok === true) {
        this.halls = data.info;
      } else {
        alert("Error getting the halls");
      }
    },
    async createhall(event) {
      event.preventDefault();
      const url = "http://localhost:3001/api/v1/Halls";
      let { data } = await this.$axios.post(url, this.hall, this.opcionesAxios);
      this.$swal.fire(data.message, "Create success");
      this.gethalls();
    },
    async updatehall(event) {
      event.preventDefault();
      const url = `http://localhost:3001/api/v1/Halls/${this.hall._id}`;
      let { data } = await this.$axios.put(url, this.hall, this.opcionesAxios);
      console.log(data);
      this.$swal.fire(data.message, "Updtae success");
      this.resetForm();
      this.gethalls();
    },
    gethall(hall) {
      this.editing = true;
      this.hall = Object.assign({}, hall.item);
    },
    resetForm() {
      this.editing = false;
      this.hall = {};
    },
    async deletehall({ item }) {
      this.$swal
        .fire({
          title: "Are you sure?",
          text: "Are you sure??",
          type: "warning",
          showCancelButton: true,
          confirmButtonColor: "#3085d6",
          cancelButtonColor: "#d33",
          confirmButtonText: "Yes",
          cancelButtonText: "No",
        })
        .then(async (result) => {
          if (result.value == true) {
            const url = `http://localhost:3001/api/v1/Halls/${item._id}`;
            let { data } = await this.$axios.delete(url, this.opcionesAxios);
            this.gethalls();
            this.$swal.fire(data.message, "Delete success");
          }
        });
    },
  },
};
</script>