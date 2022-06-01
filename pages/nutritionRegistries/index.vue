<template>
  <b-container>
    <b-col cols="4" offset="1">
      <h2 class="title">Nutrition Registry</h2>
    </b-col>
    <br />
    <!-- Creación de formulario -->
    <div class="frm">
      <b-form @submit="createRegistry" @reset="resetForm">
        <b-row class="rowForm">
          <b-col cols="4" offset="1">
            <b-form-group
              id="input-patient-identification"
              label="Patient:"
              label-for="patient"
              description="It must be a number from 7 to 11 characters"
            >
              <b-form-input
                id="patient"
                v-model="registry.patientIdentification"
                type="text"
                :disabled="editing"
                placeholder="Enter the patient identification"
                pattern="[0-9]+"
              ></b-form-input>
            </b-form-group>
          </b-col>

          <b-col cols="4" offset="1">
            <b-form-group
              id="select-room"
              label="Patient's room:"
              label-for="room"
              description="Select the room where the patient is located"
              v-if="!searching"
            >
              <b-form-select
                id="room"
                v-model="registry.roomCode"
                :options="rooms"
                text-field="option"
                value-field="roomCode"
              ></b-form-select>
            </b-form-group>
          </b-col>
        </b-row>

        <b-row class="rowForm">
          <b-col cols="4" offset="1">
            <b-form-group
              id="select-nutrition"
              label="Nutrition Type:"
              label-for="nutrition-type"
              description="Select the nutrition for the patient"
              v-if="!searching"
            >
              <b-form-select
                id="nutrition-type"
                v-model="registry.nutritionCode"
                :options="nutritionTypes"
                text-field="name"
                value-field="nutritionCode"
              ></b-form-select>
            </b-form-group>
          </b-col>
        </b-row>

        <b-row class="rowForm">
          <b-col cols="8" offset="1">
            <b-form-group
              id="textarea-comments"
              label="Comments and insights:"
              label-for="comments"
              v-if="!searching"
            >
              <b-form-textarea
                id="textarea"
                v-model="registry.comments"
                placeholder="Enter comments about the nutrition that the patient should have"
                rows="3"
                max-rows="6"
              ></b-form-textarea>
            </b-form-group>
          </b-col>
        </b-row>

        <br />
        <!-- Actions -->
        <b-row class="rowBtn">
          <b-col offset="1">
            <b-form-group v-if="!searching">
              <b-button
                type="submit"
                variant="primary"
                v-if="!editing"
                class="myPrimary formBtn"
                >Save Registry</b-button
              >
              <b-button
                variant="success"
                @click="updateRegistry"
                v-else
                class="mySuccess formBtn"
                >Modify Registry</b-button
              >
              <b-button type="reset" variant="danger" class="formBtn"
                >Clear Form</b-button
              >

              <b-button
                variant="success"
                @click="searchPatient"
                v-if="!editing"
                class="mySuccess formBtn"
                >Filter Patient</b-button
              >
            </b-form-group>

            <b-form-group v-else>
              <b-button
                variant="success"
                @click="searchRegistriesPatient"
                class="mySuccess formBtn"
                >Search</b-button
              >
              <b-button variant="danger" @click="showAll">Cancel</b-button>
            </b-form-group>

            <b-button
              variant="success"
              @click="showAll"
              v-if="onePatient"
              class="mySuccess"
              >showAll</b-button
            >
          </b-col>
        </b-row>
      </b-form>
    </div>

    <br />
    <div class="table">
      <b-table
        sticky-header
        striped
        hover
        fixed
        bordered
        :items="registries"
        :fields="headers"
        head-variant="dark"
      >
        <template #cell(options)="row">
          <b-button
            size="sm"
            @click="loadRegistry(row)"
            class="mr-2 myPrimary"
            variant="primary"
          >
            Modify
          </b-button>

          <b-button
            size="sm"
            @click="deleteRegistry(row)"
            class="mr-2"
            variant="danger"
          >
            Delete
          </b-button>
        </template>
      </b-table>
    </div>
  </b-container>
</template>
<script>
export default {
  layout: "admin-nutrition",
  data() {
    return {
      headers: [
        "patientIdentification",
        "roomCode",
        "nutritionCode",
        "comments",
        "options",
      ],
      registries: [],
      registry: {},
      rooms: [],
      nutritionTypes: [],
      editing: false,
      searching: false,
      onePatient: false,
      optionsAxios: null,
    };
  },
  beforeMount() {
    this.loadHeader();
    this.loadRegistries();
    this.loadRooms();
    this.loadNutritions();
  },
  methods: {
    loadHeader() {
      let token = localStorage.getItem("token");
      this.optionsAxios = { headers: { token } };
    },
    async loadRegistries() {
      const url = "http://localhost:3001/api/v1/nutrition-registry";
      let { data } = await this.$axios.get(url, this.optionsAxios);
      this.registries = data.info;
    },
    loadRegistry(registry) {
      this.registry = Object.assign({}, registry.item);
      this.editing = true;
    },
    async loadRooms() {
      const url = "http://localhost:3001/api/v1/rooms";
      let { data } = await this.$axios.get(url, this.optionsAxios);
      this.rooms = data.info;
      this.rooms = this.rooms.map((room) => {
        let r = "Room number " + room.roomNumber + " Hall " + room.idHall;
        return {
          ...room,
          option: r,
        };
      });
    },
    async loadNutritions() {
      const url = "http://localhost:3001/api/v1/nutrition-types";
      let { data } = await this.$axios.get(url, this.optionsAxios);
      this.nutritionTypes = data.info;
    },
    async createRegistry(event) {
      event.preventDefault();
      try {
        const url = "http://localhost:3001/api/v1/nutrition-registry";
        let { data } = await this.$axios.post(
          url,
          this.registry,
          this.optionsAxios
        );
        console.log(data);
        if (data.ok == true) {
          await this.$swal.fire("Saved!", data.message, "success");
          this.loadRegistries();
          this.resetForm();
        } else {
          await this.$swal.fire("Error!", data.info, "error");
        }
      } catch (error) {
        if (error.message == "Network Error") {
          await this.$swal.fire(
            "Sorry",
            "A problem occurred while trying to connect to the server," +
              " please reload the page or contact the administrator",
            "error"
          );
        } else if (error.response.status == 401) {
          this.$router.push("/");
          await this.$swal.fire("Error.", " La sesión expiró", "error");

        } else {
          await this.$swal.fire("Error", "contact the administrator", "error");
        }
      }
    },
    async updateRegistry(event) {
      event.preventDefault();
      try {
        const url = `http://localhost:3001/api/v1/nutrition-registry/${this.registry._id}`;
        let { data } = await this.$axios.put(
          url,
          this.registry,
          this.optionsAxios
        );
        if (data.ok == true) {
          await this.$swal.fire("Saved!", data.message, "success");
          this.loadRegistries();
          this.resetForm();
        } else {
          if (error.message == "Network Error") {
            await this.$swal.fire(
              "Sorry",
              "A problem occurred while trying to connect to the server," +
                " please reload the page or contact the administrator",
              "error"
            );
          } else {
            await this.$swal.fire("Error.", error.message, "error");
          }
        }
      } catch (error) {
        if (error.message == "Network Error") {
          await this.$swal.fire(
            "Sorry",
            "A problem occurred while trying to connect to the server," +
              " please reload the page or contact the administrator",
            "error"
          );
        } else if (error.response.status == 401) {
          this.$router.push("/");
          await this.$swal.fire("Error.", " La sesión expiró", "error");
        } else {
          await this.$swal.fire("Error", "contact the administrator", "error");
        }
      }
    },
    async deleteRegistry({ item }) {
      this.$swal
        .fire({
          title: "Are you sure?",
          text: "It can not be possible to recover the data after doing this.",
          type: "warning",
          showCancelButton: true,
          confirmButtonColor: "#525e76",
          cancelButtonColor: "#d33",
          confirmButtonText: "Yes, delete!",
          cancelButtonText: "Cancel",
        })
        .then(async (result) => {
          if (result.value) {
            try {
              const url = `http://localhost:3001/api/v1/nutrition-registry/${item._id}`;
              let { data } = await this.$axios.delete(url, this.optionsAxios);
              if (data.ok == true) {
                await this.$swal.fire("Deleted!", data.message, "success");
              } else {
                await this.$swal.fire("Error!", data.info, "error");
              }
              this.loadRegistries();
            } catch (error) {
              if (error.message == "Network Error") {
                await this.$swal.fire(
                  "Sorry",
                  "A problem occurred while trying to connect to the server," +
                    " please reload the page or contact the administrator",
                  "error"
                );
              } else if (error.response.status == 401) {
                this.$router.push("/");
                await this.$swal.fire("Error.", " La sesión expiró", "error");
              } else {
                await this.$swal.fire(
                  "Error",
                  "contact the administrator",
                  "error"
                );
              }
            }
          }
        });
    },
    async searchRegistriesPatient(event) {
      this.searching = false;
      event.preventDefault();
      try {
        const url = `http://localhost:3001/api/v1/nutrition-registry/${this.registry.patientIdentification}`;
        let { data } = await this.$axios.get(url, this.optionsAxios);
        if (data.ok == true) {
          this.onePatient = true;
          this.registries = data.info;
          await this.$swal.fire({
            title: "Consulted!",
            text:"The patient's data was correctly consulted",
            icon:"success",
            showConfirmButton:false,
            timer:1500
          });
        } else {
          await this.$swal.fire("Error!", data.info, "error");
        }
      } catch (error) {
        if (error.message == "Network Error") {
          await this.$swal.fire(
            "Sorry",
            "A problem occurred while trying to connect to the server," +
              " please reload the page or contact the administrator",
            "error"
          );
        } else if (error.response.status == 401) {
          this.$router.push("/");
          await this.$swal.fire("Error.", " La sesión expiró", "error");
        } else {
          await this.$swal.fire("Error", "contact the administrator", "error");
        }
      }
    },
    showAll(event) {
      event.preventDefault();
      this.searching = false;
      this.onePatient = false;
      this.loadRegistries();
    },
    resetForm() {
      // Reset our form values
      this.editing = false;
      this.registry = {};
    },
    searchPatient() {
      this.searching = true;
    },
  },
};
</script>
<style>
.rowForm {
  margin-top: 20px;
}

.rowBtn {
  margin-bottom: 15px;
}

.formBtn {
  margin-right: 10px;
}
</style>