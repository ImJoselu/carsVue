<template>
  <section>
    <figure>
      <v-img :src="imgRuta"></v-img>
      <figcaption>Nombre del usuario: {{ usuario.nombre }} <br>
        Correo: {{ usuario.correo_electronico }} <br>
        Nif: {{ usuario.nif }} <br>
        Edad: {{ usuario.edad }} <br>
      </figcaption>
    </figure>
    <table v-if="coches.length">
      <thead>
        <tr>
          <th>Marca del coche</th>
          <th>Modelo del coche</th>
          <th>Color del coche</th>
          <th>Matricula del coche</th>
          <th>Motor del coche</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="coche in coches" :key="coche.id">
          <span v-for="marca in marcas" :key="marca.id">
            <td v-if="marca.id == coche.marca_id">{{ marca.nombre }}</td>
          </span>
          <td>{{ coche.modelo }}</td>
          <td>{{ coche.color }}</td>
          <td>{{ coche.matricula }}</td>
          <td>{{ coche.motor }}</td>
        </tr>
      </tbody>
    </table>
    <h2 v-else>{{ usuario.nombre }} no tiene ningun coche por el momento</h2>
  </section>
</template>
<script>
import axios from "axios";

export default {
  data: function () {
    return {
      coches: [],
      usuario: {},
      marcas: [],
    }
  },
  mounted() {
    let usuarioSesion = JSON.parse(sessionStorage.getItem('usuario')).token
    axios
      .get("http://127.0.0.1:8000/api/usuarios/" + this.$route.params.id, {
        headers: {
          Authorization: "Bearer " + usuarioSesion,
        },
      })
      .then((response) => {
        this.coches = response.data.coches
        this.usuario = response.data.usuario
        this.marcas = response.data.marcas
      });
  },
  computed: {
    imgRuta() {
      return "http://127.0.0.1:8000" + this.usuario.ruta_img;
    },
  },
}
</script>
<style scope>
@import url("https://fonts.googleapis.com/css2?family=Montserrat:wght@400;500;700&display=swap");

img {
  object-fit: cover;
}

section {
  width: 100%;
  height: 100%;

  background-image: url("/../fondoPerfil.jpeg");
  background-repeat: no-repeat;
  background-size: cover;

  display: flex;
  flex-direction: row;
  align-items: center;

}

figure {
  display: inline-block;
  margin: 100px;
  /* adjust as needed */
}

figure>div:first-of-type {
  width: 400px;
  height: 400px;
  border-radius: 50%;
  vertical-align: top;
}

figure>figcaption {
  font-size: 20px;
  margin-top: 20px;
  text-align: center;
  font-family: "Montserrat", sans-serif;

}

table {
  font-family: "Montserrat", sans-serif;
  font-size: 20px;
  text-align: center;
}

th {
  padding: 10px;
  padding-bottom: 3px;
}
</style>
