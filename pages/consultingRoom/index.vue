<template>
  <b-container>
    <h1>Consulting Rooms</h1>

    <b-form @submit="createConsultingRoom" @reset="resetForm">
      <b-form-group id="igId" label="Consulting room's ID" label-for="ilId">
        <b-form-input id="iID" v-model="consultingRoom.idConsultingRoom" type="text" placeholder="Consulting Room's ID" required> </b-form-input>
      </b-form-group>

      <b-form-group id="igHall" label="Hall" label-for="ilHall">
        <b-form-input id="iHall" v-model="consultingRoom.hall" type="text" placeholder="Number of hall" required> </b-form-input>
      </b-form-group>

      <b-form-group id="igAvailability" label="Availability" label-for="ilAvailability">
        <b-form-input id="iAvailability" v-model="consultingRoom.availability" type="text" placeholder="Availability" required> </b-form-input>
      </b-form-group>

      <br />
     

      <b-button type="submit" variant="primary" v-if="!editing"
        >Create Consulting Room</b-button
      >
      <b-button variant="success" v-else @click="updateConsultingRoom"
        >Save</b-button
      >
      <b-button type="reset" variant="danger">Clear all</b-button>
    </b-form>

    <b-table striped hover :items="consultingRooms" :fields="headers">
      <template #cell(Opciones)="row">
        <b-button size="sm" @click="getConsultingRooms(row)" class="mr-2">
          Update Consulting Room
        </b-button>
        <b-button size="sm" @click="deleteConsultingRoom(row)" class="mr-2">
          Delete Consulting Room
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
      headers: ["idConsultingRoom", "hall", "availability", "Opciones"],
      consultingRooms: [],
      consultingRoom: {},
      editing: false,
      //universidades: ["Universidad de Medellin", "Universidad de Antioquia"],
      //message: "",
      opcionesAxios: null,
    };
  },
  beforeMount() {
     this.loadHeader();
    this.getConsultingRooms();
  },
  
  methods: {
    loadHeader() {
      let token = localStorage.getItem("token");
      this.opcionesAxios = { headers: { token } };
    },
    async getConsultingRooms() {
      const url = "http://localhost:3001/api/v1/consultingRooms";
      let { data } = await this.$axios.get(url, this.opcionesAxios);
      console.log(data);
      if (data.ok === true) {
        this.consultingRooms = data.info;
      } else {
        alert("Error getting the consulting rooms");
      }
    },
    async createConsultingRoom(event) {
      event.preventDefault();
      const url = "http://localhost:3001/api/v1/consultingRooms";
      let { data } = await this.$axios.post(url, this.consultingRoom, this.opcionesAxios);
      this.$swal.fire(data.message, "Create success");
      this.getConsultingRooms();
    },
    async updateConsultingRoom(event) {
      event.preventDefault();
      const url = `http://localhost:3001/api/v1/consultingRooms/${this.consultingRoom._id}`;
      let { data } = await this.$axios.put(url, this.consultingRoom, this.opcionesAxios);
      console.log(data);
      this.$swal.fire(data.message, "Updtae success");
      this.resetForm();
      this.getConsultingRooms();
    },
    getConsultingRoom(consultingRoom) {
      this.editing = true;
      this.consultingRoom = Object.assign({}, consultingRoom.item);
    },
    resetForm() {
      this.editing = false;
      this.consultingRoom = {};
    },
    async deleteConsultingRoom({ item }) {
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
            const url = `http://localhost:3001/api/v1/consultingRooms/${item._id}`;
            let { data } = await this.$axios.delete(url, this.opcionesAxios);
            this.getConsultingRooms();
            this.$swal.fire(data.message, "Delete success");
          }
        });
    },
  },
};
</script>