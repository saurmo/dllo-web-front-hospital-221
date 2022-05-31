<template>
  <div id="main">
    <b-navbar toggleable="lg" type="dark" class="navAdmin">
      <b-navbar-brand to="/admin/dashboard">
        <b-img src="~/static/hospital.png" fluid class="logo"></b-img>
        Home
      </b-navbar-brand>

      <b-navbar-toggle target="nav-collapse"></b-navbar-toggle>

      <b-collapse id="nav-collapse" is-nav>
        <b-navbar-nav>
          <b-nav-item to="/nutritionRegistries">
            <b-img src="~/static/journal.png" fluid class="icons"></b-img>
            Nutrition Registries</b-nav-item
          >
          <b-nav-item to="/nutritionTypes">
            <b-img src="~/static/plan.png" fluid class="icons"></b-img>
            Nutrition Types</b-nav-item
          >
          <b-nav-item to="/rooms">
            <b-img src="~/static/room.png" fluid class="icons"></b-img>
            Romms</b-nav-item
          >
        </b-navbar-nav>

        <!-- Right aligned nav items -->
        <b-navbar-nav class="ml-auto">
          <b-nav-item-dropdown right>
            <!-- Using 'button-content' slot -->
            <template #button-content>
              <b-avatar variant="info" src="~/static/user.png" size="md"></b-avatar>
              <em>Hi, {{ nombre }}</em>
            </template>
            <b-dropdown-item href="#">Profile</b-dropdown-item>
            <b-dropdown-item @click="signout">Sign out</b-dropdown-item>
          </b-nav-item-dropdown>
        </b-navbar-nav>
      </b-collapse>
    </b-navbar>
    <br />
    <Nuxt />
  </div>
</template>
<script>
import config from "~~/assets/config"

export default {
  beforeMount() {
    this.loadUser();
  },
  data() {
    return {
      nombre: "",
    };
  },
  methods: {
    async loadUser() {
      try {
        this.nombre = localStorage.getItem("nombre");
        let url = config.API_HOST +  "/api/v1/validate";
        let token = localStorage.getItem("token");
        let opcionesAxios = { headers: { token } };

        let { data } = await this.$axios.get(url, opcionesAxios);

        // Token correcto
        if (data.ok === false) {
          this.$router.push("/");
        }
        if (data.info.rol != "ADMIN") {
          await this.$swal.fire("Error.", "No esta autorizado.", "error");

          this.$router.push("/");
        }
      } catch (error) {
        console.log(error);
        this.$router.push("/");
      }
    },
    signout() {
      localStorage.clear();
      this.$router.push("/");
    },
  },
};
</script>
<style>
.navAdmin {
  background-color: #78938a;
}

#main {
  background-color: #92ba921c;
}

.logo {
  width: 30px;
  height: 30px;
}

.icons {
  width: 20px;
  height: 20px;
}
</style>