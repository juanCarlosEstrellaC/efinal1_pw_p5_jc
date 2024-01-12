<template>
  <div class="conteiner">
    <div v-if="juego">
    <div class="cabecera">
      <label>Puntaje: {{ puntaje }}</label>
      <label>Intento: {{ intento }}</label>
    </div>
    <div class="rectangulo">
      <Pokemon v-for="(imagen, indice) in imagenes" :key="indice" :imagen="imagen" :nombre="respuesta[indice]"/>
    </div>
    <div class="boton">
      <button @click="consumirAPI">Jugar</button>
    </div>
  </div>
    <div v-else>
      <div v-if="juegoTerminado  && puntaje < 10 && intento === maxIntentos" style="color: red">
        <p>Has utilizado tus 5 intentos. El juego ha terminado, inténtalo nuevamente.</p>
      </div>
      <div v-if="juegoTerminado && puntaje >= 10" style="color: blue">
        <p>Puntaje: {{ puntaje }}. ¡Felicitaciones! Has ganado un premio de $10,000.00.</p> 
      </div>

      <div v-if="juegoTerminado">
        <button @click="nuevoJuego">Nuevo Juego</button>
      </div>
    </div>
</div>
</template>

<script>
import Pokemon from "./components/Pokemon.vue";

export default {
  name: "App",
  components: {
    Pokemon,
  },
  data() {
    return {
      puntaje: 0,
      intento: 0,
      respuesta: [],
      imagenes: [
        "https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/other/dream-world/1.svg",
        "https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/other/dream-world/2.svg",
        "https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/other/dream-world/3.svg",
      ],
      maxIntentos: 5,
      juegoTerminado: false,
      juego: true,
    };
  },
  methods: {
    async consumirAPI() {
      this.intento++;

      const imagenesApi = [];
      const nombresApi = [];

      for (let i = 0; i < this.imagenes.length; i++) {
        const { answer, image } = await fetch("https://yesno.wtf/api").then((r) => r.json() );
        imagenesApi.push(image);
        nombresApi.push(answer);
      }

      this.imagenes = imagenesApi;
      this.respuesta = nombresApi;

      let numeroYes = 0;

      for (let i = 0; i < this.respuesta.length; i++) {
        if (this.respuesta[i] === "yes") {
          numeroYes++;
        }
      }
      if (numeroYes === 3) {
        this.puntaje = this.puntaje + 5;
      } else if (numeroYes === 2) {
        this.puntaje = this.puntaje + 2;
      } else if (numeroYes === 1) {
        this.puntaje = this.puntaje + 1;
      } else {
        this.puntaje = this.puntaje;;
      }

      if (this.intento >= this.maxIntentos || this.puntaje >= 10) {
        this.juegoTerminado = true;
        this.juego = false;
      }
    },
  },
  nuevoJuego() {
    this.puntaje = 0;
    this.intento = 0;
    this.respuesta = [];
    this.imagenes = [
        "https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/other/dream-world/1.svg",
        "https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/other/dream-world/2.svg",
        "https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/other/dream-world/3.svg",
      ],
    this.juegoTerminado = false;
    this.maxIntentos= 5;
    this.juego= true;
  },
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

.conteiner{
  display: flex;
  justify-content: center;
  align-items: center;
  flex-direction: column;
  width: 700px;
  margin: 0 auto;
  background-color: rgb(255, 255, 255);
  border: solid 1px black;
}

.cabecera{
  display: flex;
  justify-content: center;
  align-items: center;
  flex-direction: row;
  width: 700px;
  height: 50px;
  margin: 0 auto;
  background-color: rgb(255, 255, 255);
}

.cabecera label{
  margin: 0 90px;
  font-weight: bold;
}

.rectangulo {
  display: flex;
  justify-content: center;
  align-items: center;
  flex-direction: row;
  width: 700px;
  height: 450px;
  margin: 0 auto;
  background-color: rgb(255, 255, 255);
}

button{
  padding: 10px 40px;
  border: 4px solid black;
  margin: 10px;
  font-weight: bold;
}
</style>
