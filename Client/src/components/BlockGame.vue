<template>
  <!-- creating the html canvas -->
  <div>
    <canvas ref="game" width="640" height="480" style="border: 1px solid black">
    </canvas>
    <p>
      <button v-on:click="move('right')">Right</button>
      <button v-on:click="move('left')">Left</button>
      <button v-on:click="move('up')">Up</button>
      <button v-on:click="move('down')">Down</button>
    </p>
    <div>
      <label for="nombre">Ingresa tu nombre para comenzar</label>
      <input type="text" name="nombre" id="nombre" v-model='nombre'> 
    </div>
    <div v-if="nombre">
      <textarea name="chat" id="chat" cols="30" rows="10" v-model="bienvenida"></textarea>
      <input type="text" name="resp" id="resp" v-model="respuesta">
      <button @click="responder()">Responder</button>
    </div>
    
  </div>
</template>

<script>
import io from "socket.io-client"; //importing the socket.io we installed
export default {
  name: "BlockGame",
  data() {
    return {
      socket: {},
      context: {},
      position: {
        x: 0,
        y: 0,
      },
      bienvenida:'',
      nombre:'',
      respuesta:''
    };
  },
  created() {
    //this.socket = io("http://localhost:3000");
    this.socket = io("https://server-vtk5.onrender.com");
  },
  mounted() {
    this.context = this.$refs.game.getContext("2d");
    this.socket.on("position", (data) => {
      this.position = data;
      this.context.clearRect(
        0,
        0,
        this.$refs.game.width,
        this.$refs.game.height
      );
      this.context.fillRect(this.position.x, this.position.y, 20, 20);
    });
    this.socket.on("bienvenida", (data) => {
      this.bienvenida = data;
    });
  },
  methods: {
    move(direction) {
      this.socket.emit("move", direction);
    },
    responder(){
      this.socket.emit("respuesta", this.nombre+": "+this.respuesta)
    }
  },
};
</script>

<style scoped></style>
