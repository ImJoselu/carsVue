<template>
  <section>
    <img :src="imgRuta" />
    <table>
      <thead>
        <tr>
          <th>Marca del coche</th>
          <th>Modelo del coche</th>
          <th>Color del coche</th>
          <th>Matricula del coche</th>
          <th>Motor del coche</th>
          <th>Estado</th>
          <th>Precio</th>
        </tr>
      </thead>
      <tbody>
        <tr>
          <span v-for="marca in marcas" :key="marca.id">
            <td v-if="marca.id == coche.marca_id">{{ marca.nombre }}</td>
          </span>
          <td>{{ coche.modelo }}</td>
          <td>{{ coche.color }}</td>
          <td>{{ coche.matricula }}</td>
          <td>{{ coche.motor }}</td>
          <td v-if="coche.usuario_id" style="color: red; text-decoration: line-through;">Comprado</td>
          <td v-else style="color: chartreuse; text-decoration: underline;">En venta</td>
          <td>{{ precio() }}</td>
        </tr>
      </tbody>
    </table>
  </section>
</template>
<script>
import axios from "axios";
export default {
  data: function () {
    return {
      coche: {},
      marcas: [],
    }
  },
  mounted() {
    axios
      .get("http://127.0.0.1:8000/api/coches/" + this.$route.params.id).then((response) => {
        this.coche = response.data.coche
        this.marcas = response.data.marcas
      });
  },
  methods: {
    precio() {
      let cantidad = Math.random() * 1000000;
      cantidad = cantidad.toFixed(2);
      let total = cantidad + "€"
      return total;
    }
  },
  computed: {
    imgRuta() {
      return "http://127.0.0.1:8000" + this.coche.ruta_img;
    },
  },
}
</script>
<style scoped>
@import url("https://fonts.googleapis.com/css2?family=Montserrat:wght@400;500;700&display=swap");

img {
  object-fit: cover;
}

section {
  width: 100%;
  height: 100%;

  background-image: url("/../fondoCocheShow.jpg");
  background-repeat: no-repeat;
  background-size: cover;

  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;

}

section>img {
  width: 1300px !important;
  height: 480px;
  border-radius: 5%;
  border: 2px solid white;
  vertical-align: top;
  margin-bottom: 50px;
}



table {
  font-family: "Montserrat", sans-serif;
  font-size: 20px;
  text-align: center;
}

th,
td {
  padding-left: 20px;
  padding-bottom: 3px;
}
</style>
