<template>
    <b-container>
        <div>
        <b-navbar toggleable="lg" type="dark" variant="info">
            <b-navbar-brand>HOSPITAL</b-navbar-brand>
            <b-navbar-nav class="ml-auto">
                <b-nav-item href="http://localhost:3000/admin/CitasMedicas/Patients">Patients</b-nav-item>
                <b-nav-item href="http://localhost:3000/admin/CitasMedicas/AppointmentsTypes" >Appointments Types</b-nav-item>
                <b-nav-item href="http://localhost:3000/admin/CitasMedicas/Appointments" active>Appointments</b-nav-item>
            </b-navbar-nav>
        </b-navbar>
    </div>  
    <center>
        <div class="mx-auto mt-5" max-width="1280" elevation="0"></div>
        <h1>Appointments</h1>
    </center>
        <div>
            <b-modal id="modalC" hide-footer title="Schedule new appointment">
                <template>
                <div class="container">
                    <!-- Adiciona/remove a classe "show" -->
                    <div class="user-modal">
                        <form>
                        <div class="form-group">
                            <label for="name">Code</label>
                            <input required value= "" type="text" id="CodeC" class="form-control"/>
                        </div>

                        <div class="form-group">
                            <label for="patient">Patient</label>
                            <input required value= "" type="text" id="patientC" class="form-control"/>
                        </div>

                        <div class="form-group">
                            <label for="nurse">Doctor - Nurse</label>
                            <input required value= "" type="text" id="nurseC" class="form-control"/>
                        </div>

                        <div class="form-group">
                            <label for="time">Appointment Date</label>
                            <b-calendar
                            id = "AppoDateC"
                            date-format-options="{day: 'numeric', month: 'numeric', year: 'numeric'}"
                            locale="us"
                            ></b-calendar>
                        </div>

                        <div class="form-group">
                            <label for="consroom">Consulting room</label>
                            <input required value= "" type="text" id="consroomC" class="form-control"/>
                        </div>

                        <div class="form-group">
                            <label for="apptype">Appointment type</label>
                            <input required value= "" type="text" id="apptypeC" class="form-control"/>
                        </div>

                        <center>
                            <button @click="$bvModal.hide('modalC')" class="btn btn-danger">Cancel</button>
                            <button @click.prevent="createappointment()" type="submit" class="btn btn-success">Create</button> 
                        </center>
                        </form>
                    </div>
                </div>
                </template>
            </b-modal>
            <b-modal id="modalU" hide-footer title="Update appointment">
                <template>
                <div class="container">
                    <!-- Adiciona/remove a classe "show" -->
                    <div class="user-modal">
                        <form>
                        <div class="form-group">
                            <label for="name">Code</label>
                            <input required value= "" type="text" id="CodeU" class="form-control"/>
                        </div>

                        <div class="form-group">
                            <label for="patient">Patient</label>
                            <input required value= "" type="text" id="patientU" class="form-control"/>
                        </div>

                        <div class="form-group">
                            <label for="nurse">Doctor - Nurse</label>
                            <input required value= "" type="text" id="nurseU" class="form-control"/>
                        </div>

                        <div class="form-group">
                            <label for="time">Appointment Date</label>
                            <b-calendar
                            id = "AppoDateU"
                            :date-format-options="{day: 'numeric',month: 'numeric', year: 'numeric'}"
                            locale="en"
                            ></b-calendar>                         
                        </div>

                        <div class="form-group">
                            <label for="consroom">Consulting room</label>
                            <input required value= "" type="text" id="consroomU" class="form-control"/>
                        </div>

                        <div class="form-group">
                            <label for="apptype">Appointment type</label>
                            <input required value= "" type="text" id="apptypeU" class="form-control"/>
                        </div>

                        <center>
                            <button @click="$bvModal.hide('modalU')" class="btn btn-danger">Cancel</button>
                            <button @click="createappointment()" type="submit" class="btn btn-success">Create</button> 
                        </center>
                        </form>
                    </div>
                </div>
                </template>
            </b-modal>
        </div>
        <div>

        <!-- Tabela de Usuarios -->
        <b-card class="mx-auto mt-5" color="transparent" max-width="1280" elevation="0">
            <button v-b-modal.modalC pill variant="outline-danger">Schedule new appointment</button>
        </b-card>
        <div>
    <table class="table table-striped">
      <thead>
        <tr>
          <th scope="col">#</th>
          <th scope="col">Code</th>
          <th scope="col">Patient</th>
          <th scope="col">Doctor - Nurse</th>
          <th scope="col">Date</th>
          <th scope="col">Consulting room</th>
          <th scope="col">Appointment type</th>
          <th scope="col">Edit</th>
          <th scope="col">Delete</th>
        </tr>   
      </thead>
      <tbody>
        <tr v-for="(Types, index) in invoces.info" :key="Types._id" class="m-5">
            <th scope="row">{{ index + 1 }}</th>
            <td>{{ Types.idAppointments }}</td>
            <td>{{ Types.idPatient }}</td>
            <td>{{ Types.Nurse }}</td>
            <td>{{ Types.Time }}</td>
            <td>{{ Types.ConsultingRoom }}</td>
            <td>{{ Types.AppointmentType }}</td>
            <td>
                <button v-b-modal.modalU pill variant @click="UpdateData(Types._id, Types.idAppointments, Types.idPatient, Types.Nurse, Types.Time, Types.ConsultingRoom, Types.AppointmentType)" class="btn btn-success">Edit appointment</button>
            </td>
            <td>
                <button @click.prevent="deleteappointment(Types._id)" class="btn btn-danger">Delete appointment</button>
            </td>
        </tr>
      </tbody>
    </table>
            </div>
        </div>
    </b-container>
</template>

<script>
import config from "@/assets/config";

export default {
  data() {
    return {
        invoces: [],
        CodeC: "",
        patientC: "",
        nurseC: "",
        AppoDateC: "",
        consroomC: "",
        apptypeC: "",
        opcionesAxios: null,
    };
  },
  beforeMount() {
      this.loadHeader();
      this.loadappointments();
  },
    methods: {
        async loadHeader() {
            let token = localStorage.getItem("token");
            this.opcionesAxios = { headers: { token } };
        },

        async loadappointments() {
        try {
            let url = config.API_HOST + "/Appointments";
            let { data } = await this.$axios.get(url, this.opcionesAxios);
            this.invoces = data;
        } catch (error) {
            await this.$swal.fire("Api Rest Disconnected", "", "warning");
        }
        },

        async createappointment() {
        try {
            let url = config.API_HOST + "/Appointments";
            let CodeC = document.getElementById('CodeC').value
            let patientC = document.getElementById('patientC').value
            let nurseC = document.getElementById('nurseC').value
            let AppoDateC = document.getElementById('AppoDateC').value
            let consroomC = document.getElementById('consroomC').value
            let apptypeC = document.getElementById('apptypeC').value
            let { data } = await this.$axios.get(url, this.opcionesAxios);
            this.invoces = data;

            if(!CodeC && !patientC && !nurseC && !AppoDateC && !consroomC && !apptypeC){
                await this.$swal.fire("Error.", 'Empty spaces', "warning");
            } else{
                let Acumulador = 0
                await this.invoces.info.forEach(element => {
                    if(element.idAppointments == CodeC){
                        Acumulador += 1
                    }else{
                        Acumulador += 0
                    };
                })
                if(Acumulador == 0){
                    await this.$axios.post(url, {
                         idAppointments: CodeC,
                         idPatient: patientC,
                         Nurse: nurseC,
                         Time: AppoDateC,
                         ConsultingRoom: consroomC,
                         AppointmentType: apptypeC,
                    }, this.opcionesAxios)
                    await this.$swal.fire("Patient successfully registered", '', "success");
                    await this.loadappointments();
                }else{
                    await this.$swal.fire("Code Repeated :)", '', "warning");
                }
            }
        } catch (error) {
            console.log(error);
            await this.$swal.fire("Something went wrong. Try again", '', "warning");
        }
        },

        async deleteappointment(id){
            const result = await this.$swal.fire({
                title: "Delete Appointment",
                text: '¿Are you sure you want to delete the appointment?', 
                confirmButtonText: `Confirm`,                  
                showCancelButton: true,
                confirmButtonColor: '#1f9bcf',
                cancelButtonColor: '#d9534f',
                cancelButtonText: 'No',
                confirmButtonText: 'Yes, delete it'
            });
            if (!result.value) {
                return;
            }
            let url = config.API_HOST + "/Appointments/";
            let { data } = await this.$axios.delete(url+id, this.opcionesAxios);
            const response = await data.info;
            if (response) {
                await this.$swal.fire("Deleted successfully", '', "success")
                await this.loadappointments();
            } else {
                this.$toast.error('Something went wrong. Try again')
            }
        },

        async updateappointment(id) {
        try {
            let url = config.API_HOST + "/Appointments/"+id;
            let CodeC = document.getElementById('CodeU').value
            let patientC = document.getElementById('patientU').value
            let nurseC = document.getElementById('nurseU').value
            let AppoDateC = document.getElementById('AppoDateU').value
            let consroomC = document.getElementById('consroomU').value
            let apptypeC = document.getElementById('apptypeU').value

            if(!CodeC && !patientC && !nurseC && !AppoDateC && !consroomC && !apptypeC){
                await this.$swal.fire("Error.", 'Empty spaces', "warning");
            }else{ 
                await this.$axios.put(url, {
                    idAppointments: document.getElementById('CodeC').value,
                    idPatient: patientC,
                    Nurse: nurseC,
                    Time: AppoDateC,
                    ConsultingRoom: consroomC,
                    AppointmentType: apptypeC,
                }, this.opcionesAxios)
                await this.$swal.fire("Updated successfully", '', "success");
                await this.loadappointments();
            }
        } catch (error) {
            console.log(error);
        }
        },

        async UpdateData(codeU, patientU, nurseU, AppoDateU, consroomU, apptypeU) {
        this.codeC = codeU,
        this.patientC = patientU,
        this.nurseC = nurseU,
        this.AppoDateC = AppoDateU,
        this.consroomC = consroomU,
        this.apptypeC = apptypeU
      },
    }
};
</script>