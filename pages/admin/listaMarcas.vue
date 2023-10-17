<template lang="">
  <div v-if="this.usuarioSesion.esAdmin == 1" id="fondo">
    <h1>Listado de Marcas</h1>
    <v-simple-table id="tablica" style="width:1000px;">
      <thead>
        <tr>
          <th style="width: 200px;">
            ID
          </th>
          <th style="width: 200px;">
            Nombre
          </th>
          <th style="width: 200px;">
            Eliminar
          </th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="marca in marcas" :key="marca.id">
          <td>{{ marca.id }}</td>
          <td>{{ marca.nombre }}</td>
          <td>
            <button v-on:click="borrarMarca(marca.id)" id="btnEliminar"> Eliminar </button>
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
import axios from "axios";

export default {
  mounted() {
    try {
      this.usuarioSesion = JSON.parse(sessionStorage.getItem("usuario"));
      axios
        .get("http://127.0.0.1:8000/api/marcas", {
          headers: {
            Authorization: "Bearer " + this.usuarioSesion.token,
          },
        })
        .then((response) => {
          this.marcas = response.data.marcas,
            this.coches = response.data.coches

        });
    } catch (error) {
      console.log("marcas no existentes");
      console.log(error);
    }
  },

  data() {
    return {
      usuarioSesion: {},
      marcas: [],
      coches: [],
      headers: [
        {
          align: 'start',
          sortable: true,
        },
        { text: 'ID', value: 'id' },
        { text: 'Nombre', value: 'nombre' },
        { text: 'Eliminar', value: 'eliminar' },

      ]
    }
  },
  methods: {
    borrarMarca(idMarca) {
      const Swal = require('sweetalert2')
      let marcaUsada = false;
      this.coches.forEach(coche => {
        if (coche.marca_id == idMarca) {
          marcaUsada = true;
        }
      })

      if (marcaUsada) {
        Swal.fire({
          icon: 'error',
          title: 'Oops...',
          text: 'La marca que intentas eliminar tiene uno o mas coches asociado!',
        })
      } else {
        Swal.fire({
          title: '¿Quieres eliminar esta marca?',
          text: "Estas apunto de eliminar una marca",
          icon: 'warning',
          showCancelButton: true,
          confirmButtonColor: '#d33',
          cancelButtonColor: '#3085d6',
          confirmButtonText: 'Eliminar',
          cancelButtonText: 'No eliminar'
        }).then((result) => {
          if (result.isConfirmed) {
            axios({
              method: 'delete',
              url: 'http://127.0.0.1:8000/api/marcas/' + idMarca,
              headers: { Authorization: 'Bearer ' + this.usuarioSesion.token }
            })
              .then((response) => {
                if (response.status == 200) {
                  Swal.fire({
                    tittle: 'Marca Eliminada!',
                    text: "Los cambios son irreversibles",
                    icon: 'success',
                    confirmButtonText: 'Aceptar',
                  }).then((result) => {
                    if (result.isConfirmed) {
                      window.location.reload();
                    }
                  })
                };
              })
              .catch((error) => {
                console.log(error);
              });
          }
        })
      }

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

  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
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
</style>
