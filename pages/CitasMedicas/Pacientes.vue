<template>
    <b-container>
        <div>
        <b-navbar toggleable="lg" type="dark" variant="info">
            <b-navbar-brand>HOSPITAL</b-navbar-brand>
            <b-navbar-nav class="ml-auto">
                <b-nav-item href="http://localhost:3000/CitasMedicas/Patients" active>Patients</b-nav-item>
                <b-nav-item href="http://localhost:3000/CitasMedicas/AppointmentsTypes"> Appointments Types</b-nav-item>
                <b-nav-item href="http://localhost:3000/CitasMedicas/Appointments">Appointments</b-nav-item>
            </b-navbar-nav>
        </b-navbar>
    </div>
     <center>
        <div class="mx-auto mt-5" max-width="1280" elevation="0"></div>
        <h1>Patients</h1>
    </center>
        <div>
            <b-modal id="modalU" header-bg-variant=info header-text-variant=light  hide-footer title="create new user" >
            
                <template>
                <div class="container">
                    <!-- Adiciona/remove a classe "show" -->
                    <div class="user-modal">
                        <h3 class="text-center">to update</h3>
                        <form>
                        <div class="form-group">
                            <label for="idPatient">identification card</label>
                            <input  value="" type="text" id="CodeR" class="form-control"/>
                        </div>

                        <div class="form-group">
                            <label for="name">name</label>
                            <input  value="" type="text" id="NameR" class="form-control"/>
                        </div>

                        <div class="form-group">
                            <label for="lastname">lastname</label>
                            <input  value="" type="text" id="lastnameR" class="form-control"/>
                        </div>
                        <div class="form-group">
                            <label for="blodType">blodType</label>
                            <input value="" type="text" id="blodTypeR" class="form-control"/>
                        </div>
                        <div class="form-group">
                            <label for="cellphone">cellphone</label>
                            <input  value="" type="text" id="cellphoneR" class="form-control"/>
                        </div>
                        <div class="form-group">
                            <label for="address">address</label>
                            <input  value="" type="text" id="addressR" class="form-control"/>
                        </div>
                        <div class="form-group">
                            <label for="mail">mail</label>
                            <input  value="" type="text" id="mailR" class="form-control"/>
                        </div>
                        <div class="form-group">
                            <label for="gender">gender</label>
                            <input  value="" type="text" id="genderR" class="form-control"/>
                        </div>
                        <center>
                            <button @click="$bvModal.hide('modalR')" class="btn btn-danger">Cancel</button>
                            <button  @click.prevent="createPatients()" type="submit" class="btn btn-success">to update</button> 
                        </center>
                        </form>
                    </div>
                </div>
                </template>
            </b-modal>
            <b-modal id="modalR" size="lg" hide-footer title="update patients">
                <template>
                <div class="container">
                    <!-- Adiciona/remove a classe "show" -->
                    <div class="user-modal">
                        <h3 class="text-center">to update</h3>
                        <form>
                        <div class="form-group">
                            <label for="idPatient">identification card</label>
                            <input  disabled :value='idPatientA' type="text" id="CodeU" class="form-control"/>
                        </div>

                        <div class="form-group">
                            <label for="name">name</label>
                            <input :value='nameA' type="text" id="NameU" class="form-control"/>
                        </div>

                        <div class="form-group">
                            <label for="lastname">lastname</label>
                            <input  :value="lastnameA" type="text" id="lastnameU" class="form-control"/>
                        </div>
                        <div class="form-group">
                            <label for="blodType">blodType</label>
                            <input :value="blodTypeA" type="text" id="blodTypeU" class="form-control"/>
                        </div>
                        <div class="form-group">
                            <label for="cellphone">cellphone</label>
                            <input  :value="cellphoneA" type="text" id="cellphoneU" class="form-control"/>
                        </div>
                        <div class="form-group">
                            <label for="address">address</label>
                            <input  :value="addressA" type="text" id="addressU" class="form-control"/>
                        </div>
                        <div class="form-group">
                            <label for="mail">mail</label>
                            <input  :value="mailA" type="text" id="mailU" class="form-control"/>
                        </div>
                        <div class="form-group">
                            <label for="gender">gender</label>
                            <input  :value="genderA" type="text" id="genderU" class="form-control"/>
                        </div>
                        <center>
                            <button @click="$bvModal.hide('modalR')" class="btn btn-danger">Cancel</button>
                            <button  @click.prevent="updatePatients(idA)" type="submit" class="btn btn-success">to update</button> 
                        </center>
                        </form>
                    </div>
                </div>
                </template>
            </b-modal>

        </div>
        <div>
            <b-card class="mx-auto mt-5" color="transparent" max-width="1300" elevation="0">
                <button v-b-modal.modalU pill variant="outline-danger">Create new patients</button>
            </b-card>
            <div>
            
<!-- Tabela de Usuários -->
    <table class="table table-striped">
      <thead>
        <tr>
          <th scope="col">#</th>
          <th scope="col">identification card</th>
          <th scope="col">name</th>
          <th scope="col">lastname</th>
          <th scope="col">blodType</th>
          <th scope="col">cellphone</th>
          <th scope="col">address</th>
          <th scope="col">mail</th>
          <th scope="col">gender</th>
          <th scope="col">Edit</th>
          <th scope="col">Delete</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(Types, index) in invoces" :key="Types._id" class="m-5">
            <th scope="row">{{ index + 1 }}</th>
            <td>{{ Types.idPatient }}</td>
            <td>{{ Types.name }}</td>
            <td>{{ Types.lastname }}</td>
             <td>{{ Types.blodType }}</td>
            <td>{{ Types.cellphone }}</td>
            <td>{{ Types.address }}</td>  
             <td>{{ Types.mail }}</td>
            <td>{{ Types.gender }}</td>
            <td>
                <b-card class="mx-auto mt-5" color="#000000" max-width="900" elevation="100">
                <button v-b-modal.modalR pill variant @click="Probando(Types._id, Types.idPatient, Types.name, Types.lastname, Types.blodType, Types.cellphone, Types.address, Types.mail, Types.gender)" class="btn btn-success">Editar</button>
                </b-card>
            </td>
            <td>
                <b-card class="mx-auto mt-5" color="#000000" max-width="900" elevation="100">
                <button @click.prevent="deletePatients(Types._id)" class="btn btn-sm  btn-danger">delete</button>
                </b-card>
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
      idA :"",
      idPatientA :"",
      nameA :"",
      lastnameA :"",
      blodTypeA :"",
      cellphoneA :"",
      addressA :"",
      mailA : "",
      genderA : "",
      opcionesAxios: null,
     
    };
  },
  created() {
    this.loadHeader();
    this.loadPatients();
  },
    methods: {
        async loadHeader() {
            let token = localStorage.getItem("token");
            this.opcionesAxios = { headers: { token } };
        },
        async loadAppointmentsTypes() {
        try {
            let url = config.API_HOST + "/Patients";
            let { data } = await this.$axios.get(url, this.opcionesAxios);
            this.invoces = data.info;
            
        } catch (error) {
            console.log(error);
        }
        },

        async deletePatients(id){
            const result = await this.$swal.fire({
                title: "Delete Patients Types",
                text: '¿Confirma eliminar el registro?', 
                confirmButtonText: 'Confirmar',
                showCancelButton: true,
                confirmButtonColor: '#1f9bcf',
                cancelButtonColor: '#d9534f',
                cancelButtonText: 'No',
                confirmButtonText: 'Yes, delete it'
            });
            if (!result.value) {
                return;
            }
            let url = config.API_HOST + "/Patients/";
            let { data } = await this.$axios.delete(url+id, this.opcionesAxios);
            const response = await data.info;
            if (response) {
                this.$swal.fire("Deleted good", '', "success")
                await this.loadPatients();
            } else {
                this.$swal.fire('Something went wrong. Try again',"","warning")
            }
        },
        async createPatients() {
            try {
                let url = config.API_HOST + "/Patients";
                let idpatient1 = document.getElementById('CodeR').value
                let name1 =document.getElementById('NameR').value
                let lastname1 = document.getElementById('lastnameR').value
                let blodType1 = document.getElementById('blodTypeR').value
                let cellphone1=document.getElementById('cellphoneR').value
                let address1=document.getElementById('addressR').value
                let mail1= document.getElementById('mailR').value
                let gender1=document.getElementById('genderR').value
                let { data } = await this.$axios.get(url, this.opcionesAxios);
                this.invoces = data;

                if(!idpatient1 && !name1 && !lastname1 && !blodType1 && !cellphone1 && !address1 && !mail1 && !gender1){ 
                    await this.$swal.fire("Error.",'campos vacios', "warning");
                }else {
                    let Acumulador = 0
                    await this.invoces.info.forEach(element => {
                        if(element.idPatient == idpatient1 ){
                            Acumulador += 1
                    }else{
                        Acumulador += 0
                    };
                })
                if(Acumulador == 0){ 
                    await this.$axios.post(url, {
                        idPatient: idpatient1,
                        name: name1,
                        lastname: lastname1,
                        blodType: blodType1,
                        cellphone:cellphone1,
                        address: address1,
                        mail: mail1,
                        gender: gender1,

                    },this.opcionesAxios)
                    await this.$swal.fire("REgistrado Correctamente", '', "success");
                    await this.loadPatients();
                }else{
                    await this.$swal.fire("Codigo Repetido :)", '', "warning");
                 }
            }

        }catch (error) {
            console.log(error);
            await this.$swal.fire("Error.", error, "warning");
        }
        },
        async updatePatients(id) {
        try {
            let url = config.API_HOST + "/Patients/"+id;
            let idpatient1 = document.getElementById('CodeU').value
            let name1 =document.getElementById('NameU').value
            let lastname1 = document.getElementById('lastnameU').value
            let blodType1 = document.getElementById('blodTypeU').value
            let cellphone1=document.getElementById('cellphoneU').value
            let address1=document.getElementById('addressU').value
            let mail1= document.getElementById('mailU').value
            let gender1=document.getElementById('genderU').value

            if(!name1 && !lastname1 && !blodType1 && !cellphone1 && !address1 && !mail1 && !gender1){
                await this.$swal.fire("Error.", 'Campos Vacios', "warning");
            }else{ 
                await this.$axios.put(url, {
                    idPatient: document.getElementById('CodeU').value,
                    name: name1,
                    lastname: lastname1,
                    blodType: blodType1,
                    cellphone:cellphone1,
                    address: address1,
                    mail: mail1,
                    gender: gender1,
                    }, this.opcionesAxios)

                await this.$swal.fire("Registrado Correctamente", '', "success");
                await this.loadAppointmentsTypes();
            }


        } catch (error) {
            console.log(error);
        }
        },
        async Probando(idU,idPatientU,nameU, lastnameU,blodTypeU,cellphoneU,addressU,mailU,genderU) {
        this.idA = idU,
        this.idPatientA =  idPatientU,
        this.nameA = nameU,
        this.lastnameA = lastnameU,
        this.blodTypeA = blodTypeU,
        this.cellphoneA = cellphoneU,
        this.addressA = addressU,
        this.mailA = mailU,
        this.genderA = genderU
        },
    },
};
</script>
