<template>
  <div id="message">
    <b-jumbotron bg-variant="light" border-variant="dark">
      <template #header>
        Final task
        <b-img src="~/static/settings.png" fluid id="mainImg"></b-img>
      </template>

      <template #lead>
        This is a Web page under development for the nutrition administration module of a hospital
      </template>

      <hr class="my-4" />

      <p>Developers</p>

      <ul>
        <li>Jesssica Bilbao Restrepo</li>
        <li>Santiago Gómez Montoya</li>
        <li>Diego Alejandro Durango Garro</li>
      </ul>
      <br />
      <p>Aditional credits</p>
      <div>
        Icons made by
        <a href="https://www.freepik.com" title="Freepik"> Freepik </a> from
        <a href="https://www.flaticon.com/" title="Flaticon"
          >www.flaticon.com'</a
        >
      </div>
    </b-jumbotron>
  </div>
</template>
<script>
export default {
  layout: "admin-nutrition",
  data() {
    return {};
  },
  methods: {},
};
</script>
<style>
#message {
  margin: 30px 80px;
  color: #525e76;
}

#mainImg{
    width: 100px;
    height: 100px;
}
</style>
