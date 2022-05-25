<template>
  <b-container class="bv-example-row">
    <div style="padding: 50px">
      <input type="text" placeholder="Ingrese su nombre" v-model="username" />
      <br />
      <button @click="connectSocket">Ingresar al chat</button>

      <b-form
        @submit="sendMessage"
        action="javascript:void(0)"
        v-if="userConnected"
      >
        <b-form-group
          label="Mensaje"
          label-for="inputMessage"
          description="Ingrese un mensaje"
        >
          <b-form-input
            id="inputMessage"
            v-model="message"
            type="text"
            required
          ></b-form-input>
        </b-form-group>
        <br />
        <b-button type="submit" variant="primary">Enviar mensaje</b-button>
      </b-form>

      <b-row>
        <b-col>
          <h2>Usuarios conectados</h2>
          <ul>
            <li v-for="(user, index) in users" :key="index">
              {{ user.username }}
            </li>
          </ul>
        </b-col>
        <b-col>
          <h2>Chat</h2>
          <ul>
            <li v-for="(message, index) in messages" :key="index">
              <span style="color: red"> {{ message.username }} </span> :
              {{ message.message }}
            </li>
          </ul>
        </b-col>
      </b-row>
    </div>
  </b-container>
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
      userConnected: false,
      message: "",
      messages: [],
    };
  },
  methods: {
    loadPage() {},
    connectSocket() {
      this.socket = io(config.SOCKET_HOST);
      this.socket.emit("nuevo-usuario", { username: this.username });
      this.onUsers();
      this.onMessage();
    },

    onUsers() {
      this.socket.on("usuarios-conectados", (data) => {
        this.userConnected = true;
        this.users = data;
      });
    },

    onMessage() {
      this.socket.on("pushMessages", (data) => {
        this.messages = data;
      });
    },

    sendMessage(event) {
      console.log(event);
      event.preventDefault();
      try {
        console.log(this.socket);
        this.socket.emit("onMessage", {
          username: this.username,
          message: this.message,
        });
      } catch (error) {
        console.log(error);
      }
    },
  },
};
</script>