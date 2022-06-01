<template>
    <b-container>

    <div>
        <b-navbar toggleable="lg" type="dark" variant="info">
            <b-navbar-brand>HOSPITAL</b-navbar-brand>

            <b-navbar-nav class="ml-auto">
                <b-nav-item href="http://localhost:3000/CitasMedicas/Patients">Patients</b-nav-item>
                <b-nav-item href="http://localhost:3000/CitasMedicas/AppointmentsTypes" active>Appointments Types</b-nav-item>
                <b-nav-item href="http://localhost:3000/CitasMedicas/Appointments">Appointments</b-nav-item>
            </b-navbar-nav>

        </b-navbar>
    </div>

    <center>
        <div class="mx-auto mt-5" max-width="1280" elevation="0"></div>
        <h1>Appointments Types</h1>
    </center>
    <div>
        <b-modal id="modalR" header-bg-variant=info header-text-variant=light hide-footer title="Register Appointments Types">
            <template>
            <div class="container">
                <div class="user-modal">
                    <form>
                    <div class="form-group">
                        <label for="name">Code</label>
                        <input required value="" type="text" id="CodeR" class="form-control"/>
                    </div>
                
                    <div class="form-group">
                        <label for="email">Name</label>
                        <input required value="" type="text" id="NameR" class="form-control"/>
                    </div>
                    
                    <div class="form-group">
                        <label for="password">Description</label>
                        <input required value="" type="text" id="passwordR" class="form-control"/>
                    </div>
                    <center>
                        <button @click="$bvModal.hide('modalR')" class="btn btn-danger">Cancelar</button>
                        <button  @click.prevent="createAppointmentsTypes()" class="btn btn-success">Crear</button> 
                    </center>
                    </form>
                </div>
            </div>
            </template>
        </b-modal>
        <b-modal id="modalU" header-bg-variant=info header-text-variant=light hide-footer title="Update Appointments Types">
            <template>
            <div class="container">
                <div  class="user-modal">
                    <h3 class="text-center"></h3>
                    <form>  
                    <div class="form-group">
                        <label for="name">Code</label>
                        <input disabled :value='codeA'   type="text" id="CodeU" class="form-control"/>
                    </div>
                
                    <div class="form-group">
                        <label for="email">Name</label>
                        <input required :value='NameA' type="text" id="NameU" class="form-control"/>
                    </div>
                    
                    <div class="form-group">
                        <label for="password">Description</label>
                        <input required :value='DescriptionA' type="text" id="passwordU" class="form-control"/>
                    </div>
                    <center>
                        <button @click="$bvModal.hide('modalU')" class="btn btn-danger">Cancelar</button>
                        <button @click.prevent="updateAppointmentsTypes(idA)" type="submit" class="btn btn-success">Crear</button> 
                    </center>
                    </form>
                </div>
            </div>
            </template>
        </b-modal>
    </div>

    <!-- Tabela de Usuários -->
    <b-card class="mx-auto mt-5" max-width="1280" elevation="0">
        <b-button v-b-modal.modalR pill variant="primary" class="float-right" >Create</b-button>
        <div class="mx-auto mt-5" max-width="1280" elevation="0"></div>

        <table class="table table-striped">
        <thead>
            <tr>
            <th scope="col">#</th>
            <th scope="col">Code</th>
            <th scope="col">Name</th>
            <th scope="col">Description</th>
            <th scope="col">Edit</th>
            <th scope="col">elete</th>
            </tr>
        </thead>
        <tbody>
            <tr v-for="(Types, index) in invoces.info" :key="Types._id" class="m-5">
                <th scope="row">{{ index + 1 }}</th>
                <td>{{ Types.idAppointmentType }}</td>
                <td>{{ Types.name }}</td>
                <td>{{ Types.description }}</td>
                <td>
                    <button v-b-modal.modalU pill variant @click="Probando(Types._id, Types.idAppointmentType, Types.name, Types.description)" class="btn btn-success">Edit</button>
                </td>
                <td>
                    <button @click.prevent="deleteAppointmentsTypes(Types._id)" class="btn btn-danger">Delete</button>
                </td>
            </tr>
        </tbody>
        </table>
    </b-card>
    </b-container>
</template>
<script>
import config from "@/assets/config";

export default {
  data() {
    return {
        invoces: [],
        idA: "",
        codeA: "",
        NameA: "",
        DescriptionA: "" 
    };
  },
  beforeMount() {
    this.loadAppointmentsTypes();
  },
    methods: {
        async loadAppointmentsTypes() {
        try {
            let url = config.API_HOST + "/AppointmentsTypes";
            let { data } = await this.$axios.get(url);
            this.invoces = data;
            } catch (error) {
                await this.$swal.fire("API Rest Disconnected", "", "Danger");
            }
        },
        async createAppointmentsTypes() {
        try {
            let url = config.API_HOST + "/AppointmentsTypes";
            let CodeR = document.getElementById('CodeR').value
            let NameR = document.getElementById('NameR').value
            let passwordR = document.getElementById('passwordR').value
            let { data } = await this.$axios.get(url);
            this.invoces = data;

            if(!CodeR && !NameR && !passwordR){
                await this.$swal.fire("Empty Fields", '', "warning");
            } else{
                let Acumulador = 0
                await this.invoces.info.forEach(element => {
                    if(element.idAppointmentType == CodeR){
                        Acumulador += 1
                    }else{
                        Acumulador += 0
                    };
                })
                if(Acumulador == 0){
                    await this.$axios.post(url, {
                         idAppointmentType: CodeR,
                         name: NameR,
                         description: passwordR,
                    })
                    await this.$swal.fire("Successful Registration", '', "success");
                    await this.loadAppointmentsTypes();
                }else{
                    await this.$swal.fire("Repeated Code", '', "warning");
                }
            }
        } catch (error) {
            console.log(error);
            await this.$swal.fire("Something went wrong. Try again", '', "warning");
        }
        },
        async updateAppointmentsTypes(id) {
        try {
            let url = config.API_HOST + "/AppointmentsTypes/"+id;
            let nameU = document.getElementById('NameU').value
            let passwordU = document.getElementById('passwordU').value

            if(!nameU && !passwordU){
                await this.$swal.fire("Empty Fields", '', "warning");
            }else{ 
                await this.$axios.put(url, {
                    idAppointmentType: document.getElementById('CodeU').value,
                    name: nameU,
                    description: passwordU
                })
                await this.$swal.fire("Successful Registration", '', "success");
                await this.loadAppointmentsTypes();
            }


        } catch (error) {
            console.log(error);
        }
        },
        async deleteAppointmentsTypes(id){
            const result = await this.$swal.fire({
                title: "Delete Appointments Types",
                text: '¿Confirm to Delete the Appointments Types?', 
                confirmButtonText: `Confirmar`,                  
                showCancelButton: true,
                confirmButtonColor: '#1f9bcf',
                cancelButtonColor: '#d9534f',
                cancelButtonText: 'No',
                confirmButtonText: 'Yes, delete it'
            });

                if (!result.value) {
                return;
                }
                let url = config.API_HOST + "/AppointmentsTypes/";
                let { data } = await this.$axios.delete(url+id);
                const response = await data.info;
                if (response) {
                   await this.$swal.fire("Successful deletion", '', "success")
                    await this.loadAppointmentsTypes();
                } else {
                    await this.$swal.fire("Something went wrong. Try again", '', "warning");
                }

        },
        async Probando(idU, codeU, nameU, descriptionU) {
        this.idA = idU,
        this.codeA = codeU,
        this.NameA = nameU,
        this.DescriptionA = descriptionU
      },
    },
};
</script>
