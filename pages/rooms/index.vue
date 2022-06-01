<template>
  <b-container>
    <h1>Rooms</h1>
    <!-- Creacion del formulario -->
    <b-form @submit="createroom" @reset="resetForm">
      <div class="inputs">
        <div class="input-data">
          <b-form-group
            id="input-group-1"
            label="Room code:"
            label-for="input-1"
          >
            <b-form-input
              id="input-1"
              :disabled="editing"
              v-model="room.roomCode"
              placeholder="Enter room code"
              type="number"
              pattern="[0-9]+"
            ></b-form-input>
          </b-form-group>
        </div>
        <div class="input-data">
          <b-form-group id="input-group-2" label="Number:" label-for="input-2">
            <b-form-input
              id="input-2"
              :disabled="editing"
              v-model="room.roomNumber"
              placeholder="Enter number of room"
              type="number"
              pattern="[0-9]+"
            ></b-form-input>
          </b-form-group>
        </div>
        <div class="input-data">
          <b-form-group
            id="input-group-2"
            label="id Hall:"
            description="Select Id Hall"
            label-for="input-2"
          >
           <b-form-select
                id="hall-type"
                v-model="room.idHall"                
                :options="hall"
                
                text-field="idConsultingRoom"
                value-field="idConsultingRoom"
              ></b-form-select>
          </b-form-group>
        </div>
      </div>
      <br />
      <!-- Actions -->
      <b-button type="submit" variant="success" v-if="!editing"
        >CREATE</b-button
      >
      <b-button variant="success" v-else @click="updaterooms"
        >SAVE</b-button
      >
      <b-button type="reset" variant="danger">CANCEL</b-button>
    </b-form>

    <b-table
      bordered
      striped
      hover
      :items="rooms"
      :fields="headers"
      class="table-data"
    >
      <template #cell(options)="row">
        <b-button
          size="sm"
          @click="loadroom(row)"
          class="mr-2"
          variant="primary"
        >
          UPDATE
        </b-button>
        <b-button
          size="sm"
          @click="deleteroom(row)"
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
        "roomCode",
        "roomNumber",
        "idHall",
        "options",
      ],
      rooms: [],
      room: {},
      hall:[],
      editing: false,
      message: "",
      opcionesAxios: null,
    };
  },
  // Método antes de que cargue la página
  beforeMount() {
    this.loadHeader();
    this.loadrooms();
    this.loadhalls();
  },
  // Métodos
  methods: {
    loadHeader() {
      let token = localStorage.getItem("token");
      this.opcionesAxios = { headers: { token } };
    },
    async loadrooms() {
      // Cargar los usuarios de la base de datos
      const url = "http://localhost:3001/api/v1/rooms";
      let { data } = await this.$axios.get(url, this.opcionesAxios);
      console.log(data);
      if (data.ok === true) {
        this.rooms = data.info;
      } else {
        this.$swal.fire("Error fetching data", data.message, "error");
      }
    },
    async loadhalls() {
      const url = "http://localhost:3001/api/v1/consultingRooms";
      let { data } = await this.$axios.get(url, this.opcionesAxios);
      this.hall = data.info;
    },
    async createroom(event) {
      event.preventDefault();
      const url = "http://localhost:3001/api/v1/rooms";
      let { data } = await this.$axios.post(
        url,
        this.room,
        this.opcionesAxios
      );
      this.message = data.message;
      if (data.ok == true) {
        this.$swal.fire(data.message, data.info, "success");
      } else {
        this.$swal.fire(data.message, data.info, "error");
      }
      this.loadrooms();
      this.resetForm();
    },
    async updaterooms(event) {
      event.preventDefault();
      try {
        this.editing = true;
        const url = `http://localhost:3001/api/v1/rooms`;
        const dataToUpdate = {
          roomCode: this.room.roomCode,
          roomNumber: this.room.roomNumber,
          idHall: this.room.idHall,
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
        this.loadrooms();
      } catch (error) {
        this.$swal.fire(
          "ERROR",
          "An error has ocurred with the server",
          "error"
        );
      }
    },
    loadroom(room) {
      this.editing = true;
      this.room = Object.assign({}, room.item);
    },
    resetForm() {
      this.editing = false;
      this.room = {};
    },
    async deleteroom({ item }) {
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
            const url = `http://localhost:3001/api/v1/rooms/${item.roomCode}`;
            let { data } = await this.$axios.delete(url, this.opcionesAxios);
            if (data.ok == true) {
              this.$swal.fire(data.message, data.info, "success");
            } else {
              this.$swal.fire(data.message, data.info, "error");
            }
            this.loadrooms();
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
