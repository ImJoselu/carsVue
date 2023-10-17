<template>
  <div id="fondo" v-if="this.usuarioSesion && this.usuarioSesion.esAdmin == 1">
    <h1>Listado de Usuarios</h1>
    <v-simple-table id="tablica">
      <thead>
        <tr>
          <th id="idHead">
            ID
          </th>
          <th id="nombreHead">
            Nombre
          </th>
          <th id="edadHead">
            Edad
          </th>
          <th id="nifHead">
            NIF
          </th>
          <th id="correoHead">
            Correo Electronico
          </th>
          <th id="opciones">
            Opciones
          </th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="usuario in usuarios" :key="usuario.id">
          <td v-if="!usuario.es_admin">{{ usuario.id }}</td>
          <td v-if="!usuario.es_admin">{{ usuario.nombre }}</td>
          <td v-if="!usuario.es_admin">{{ usuario.edad }}</td>
          <td v-if="!usuario.es_admin">{{ usuario.nif }}</td>
          <td v-if="!usuario.es_admin">{{ usuario.correo_electronico }}</td>
          <td v-if="!usuario.es_admin">
            <AppBoton :href="'/usuarios/' + usuario.id" value="Detalles" color="#008000" />
            <AppBoton :href="'/usuarios/edit/' + usuario.id" value="Editar" color="#FFA500" />
            <button v-on:click="borrarUser(usuario.id)" id="btnEliminar"> Eliminar </button>
          </td>
        </tr>
      </tbody>
    </v-simple-table>
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
  mounted() {
    try {
      this.usuarioSesion = JSON.parse(sessionStorage.getItem("usuario"));
      axios
        .get("http://127.0.0.1:8000/api/usuarios", {
          headers: {
            Authorization: "Bearer " + this.usuarioSesion.token,
          },
        })
        .then((response) => {
          this.usuarios = response.data
        });
    } catch (error) {
      console.log("usuario no existente");
      console.log(error);
    }
  },

  methods: {
    borrarUser(id) {
      axios({
        method: 'delete',
        url: 'http://127.0.0.1:8000/api/usuarios/' + id,
        headers: { Authorization: "Bearer " + this.usuarioSesion.token }
      })
        .then((response) => {
          if (response.status == 200) {
            this.$router.push("/admin/listaUsuariosEliminados")
          };
        })
        .catch((error) => {
          console.log(error);
        });
    },
  },

  components: {
    AppBoton,
  },

  data() {
    return {
      usuarios: [],
      usuarioSesion: {},
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

#btnEliminar {
  width: 100px;
  height: 36px;
  margin: 3px;
  background-color: #FF0000;
  border-radius: 5%;
  align-items: center;
  border-radius: 4px;
  display: inline-flex;
  flex: 0 0 auto;
  font-weight: 500;
  letter-spacing: 0.0892857143em;
  justify-content: center;
  outline: 0;
  position: relative;
  text-indent: 0.0892857143em;
  text-transform: uppercase;
  transition-duration: 0.28s;
  transition-property: box-shadow, transform, opacity;
  transition-timing-function: cubic-bezier(0.4, 0, 0.2, 1);
  vertical-align: middle;
  white-space: nowrap;
}

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

#tablica {
  margin: 0 auto;
  padding-top: 20px;
  padding-bottom: 20px;
  padding-left: 100px;
  padding-right: 100px;
}

td , th {
  text-align: center;
  font-size: 16px !important;
  font-family: 'Cinzel', serif !important;

}

#idHead {
  font-size: 17px;
  text-align: center;
}

#nombreHead {
  font-size: 17px;
  text-align: center;
}

#edadHead {
  font-size: 17px;
  text-align: center;
}

#nifHead {
  font-size: 17px;
  text-align: center;
}

#correoHead {
  font-size: 17px;
  text-align: center;
}

#opciones {
  font-size: 17px;
  text-align: center;
}
</style>
