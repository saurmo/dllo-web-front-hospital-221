<template>
  <div style="padding: 50px">
    <input type="text" placeholder="Ingrese su nombre" v-model="username" />
    <br />
    <button @click="connectSocket">Ingresar</button>

    <ul>
      <li v-for="(user, index) in users" :key="index">
        {{ user.username }}
      </li>
    </ul>
  </div>
</template>

<script>
import config from "@/assets/config";
import { io } from "socket.io-client";
export default {
  beforeMount() {
    this.loadPage();
  },
  data() {
    return {
      users: [],
      socket: null,
      username: null,
    };
  },
  methods: {
    loadPage() {},
    connectSocket() {
      this.socket = io(config.SOCKET_HOST);
      this.socket.emit("nuevo-usuario", { username: this.username });
      this.socket.on("usuarios-conectados", (data) => {
        this.users = data;
      });
    },
  },
};
</script>