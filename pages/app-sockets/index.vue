<template>
  <div>
    <ul>
      <li v-for="(user, index) in users" :key="index">
        {{ user }}
      </li>
    </ul>
  </div>
</template>

<script>

import { io } from "socket.io-client";
export default {
  beforeMount() {
    this.loadPage();
  },
  data() {
    return {
      users: [],
      socket:null
    };
  },
  methods: {
    loadPage() {
      console.log(io);
      
      this.socket=io()
    },
  },
};
</script>