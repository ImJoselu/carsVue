<template>
  <div id="fondo" v-if="this.usuarioSesion && this.usuarioSesion.esAdmin == 1">
    <h1>Listado de Usuarios Eliminados</h1>
    <h2 v-if="usuarios.length <= 0">No hay Usuarios Eliminados por el momento</h2>
    <v-simple-table id="tablica" v-else>
      <thead>
        <tr>
          <th id="idHead">
            ID
          </th>
          <th id="nombreHead">
            Nombre
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
          <td>{{ usuario.id }}</td>
          <td>{{ usuario.nombre }}</td>
          <td>{{ usuario.correo_electronico }}</td>
          <td>
            <button v-on:click="restaurarUser(usuario.id)" id="btnRestaurar"> Restaurar </button>
          </td>
        </tr>
      </tbody>
    </v-simple-table>
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
        .get("http://127.0.0.1:8000/api/usuariosEliminados", {
          headers: {
            Authorization: "Bearer " + this.usuarioSesion.token,
          },
        })
        .then((response) => {
          this.usuarios = response.data;
        });
    } catch (error) {
      console.log("usuario no existente");
      console.log(error);
    }


  },

  components: {
    AppBoton,
  },

  data() {
    return {
      usuarios: [],
      usuarioSesion: {},
    }
  },

  methods: {
    restaurarUser(id) {
      axios({
        method: 'post',
        url: 'http://127.0.0.1:8000/api/usuariosEliminados/' + id + "/restaurar",
        headers: { Authorization: "Bearer " + this.usuarioSesion.token }
      })
        .then((response) => {
          if (response.status == 200) {
            this.$router.push("/admin/listaUsuarios");

          };
        })
        .catch((error) => {
          console.log(error);
        });
    }
  },
}
</script>
<style>
@import url('https://fonts.googleapis.com/css2?family=Cinzel:wght@500&display=swap');
@import url('https://fonts.googleapis.com/css2?family=Press+Start+2P&display=swap');

#btnRestaurar {
  width: 50%;
  height: 36px;
  margin: 3px;
  background-color: #008000;
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
}

h1,
h2 {
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

td,
th {
  text-align: center;
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
}</style>
