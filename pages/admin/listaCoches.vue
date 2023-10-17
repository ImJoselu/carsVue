<template>
  <div v-if="this.usuarioSesion.esAdmin == 1" id="fondo">
    <h1>Listado de Coches</h1>
    <v-simple-table id="tablica" v-if="this.usuarioSesion.esAdmin == 1">
      <thead>
        <tr>
          <th>
            ID
          </th>
          <th>
            Modelo
          </th>
          <th>
            Color
          </th>
          <th>
            Matricula
          </th>
          <th>
            Motor
          </th>
          <th>
            Estado
          </th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="coche in coches" :key="coche.id">
          <td v-if="!coche.usuario_id != null">{{ coche.id }}</td>
          <td v-if="!coche.usuario_id != null">{{ coche.modelo }}</td>
          <td v-if="!coche.usuario_id != null">{{ coche.color }}</td>
          <td v-if="!coche.usuario_id != null">{{ coche.matricula }}</td>
          <td v-if="!coche.usuario_id != null">{{ coche.motor }}</td>
          <td v-if="!coche.usuario_id != null" id="estado">{{ estado(coche.usuario_id) }}</td>
        </tr>
      </tbody>
    </v-simple-table>
    <div v-else>
      <div class="mt-7 max-w-sm mx-auto textcenter card">
        <h1>Error 404</h1>
        <a href="/">Back to home</a>
      </div>
    </div>
    <!-- DATA TABLE
    <v-spacer></v-spacer>
    <v-data-table :headers="headers" :items="usuarios" :items-per-page="5" class="elevation-1" >
      <template #[`item.opciones`]="{usuario}" >
        <div>
          <AppBoton :href="'/usuarios/' + usuarios.id" value="Detalles" color="#008000" />
          <AppBoton :href="'/usuarios/edit/' + usuarios.id" value="Editar" color="#FFA500" />
          <button v-on:click="borrarUser(usuarios.id)" id="btnEliminar"> Eliminar </button>
        </div>
      </template>
    </v-data-table>
    -->
  </div>
  <div v-else class="mt-7 max-w-sm mx-auto textcenter card">
    <h1>Error 404</h1>
    <a href="/">Back to home</a>
  </div>
</template>
<script>
import AppBoton from "@/components/AppBoton.vue";
import axios from "axios";

export default {
  asyncData({ params }) {
    return axios.get("http://127.0.0.1:8000/api/coches").then((response) => {
      return {
        coches: response.data.coches,
        marcas: response.data.marcas,
        usuarios: response.data.usuarios,
      };
    });
  },
  mounted() {
    this.usuarioSesion = JSON.parse(sessionStorage.getItem("usuario"));
  },

  methods: {
    estado(idUser) {
      if (idUser) {
        return "Vendido";
      } else {
        return "A la venta";
      }
    }
  },

  components: {
    AppBoton,
  },

  data() {
    return {
      usuarioSesion: {},
      coches: [],
      marcas: {},

      /*
            headers: [
              {
                align: 'start',
                sortable: true,
              },
              { text: 'ID', value: 'id' },
              { text: 'Nombre', value: 'nombre' },
              { text: 'Edad', value: 'edad' },
              { text: 'NIF', value: 'nif' },
              { text: 'Correo Electronico', value: 'correo_electronico' },
              { text: 'Opciones', value: 'opciones' },
            ]*/
    }
  },

}
</script>
<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Cinzel:wght@500&display=swap');
@import url('https://fonts.googleapis.com/css2?family=Press+Start+2P&display=swap');

#fondo {
  width: 100%;
  height: 100%;
  background: none;
}

h1 {
  text-align: center;
  margin-top: 20px;
  margin-bottom: 20px;
  font-family: 'Press Start 2P', cursive;
}


td,
th {
  text-align: center !important;
  font-size: 16px !important;
  font-family: 'Cinzel', serif !important;

}

#tablica {
  text-align: center;
}
</style>
