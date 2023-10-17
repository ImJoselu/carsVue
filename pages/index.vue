<template>
  <div id="contenedor">
    <!--
    <section id="filtro">
      <v-select v-model="selected" label="Filtro por Marcas" :items="marcas" item-text="nombre" item-value="nombre">
      </v-select>
      <v-btn @click.prevent="filtrar">FILTRAR</v-btn>
      <v-select v-model="selectedEstado" label="Filtro por Estado" :items="estadosCoche"> </v-select>
      <v-btn @click.prevent="filtrarPorEstado">FILTRAR</v-btn>
    </section>
    -->
    <section id="filtro">
      <div>
        <v-select id="selectUno" v-model="selected" label="Filtro por Marcas" :items="marcas" item-text="nombre"
          item-value="nombre"></v-select>
      </div>
      <div>
        <v-select id="selectDos" v-model="selectedEstado" label="Filtro por Estado" :items="estadosCoche"> </v-select>
      </div>
      <v-btn @click.prevent="filtroCompleto">FILTRAR</v-btn>
    </section>
    <nav id="paginacionCoches">

      <a id="anterior" href="#" aria-label="Previous" @click.prevent="pag -= 1" :hidden="escondido()">
        <span aria-hidden="true"><v-icon class="flechas" style="font-size: 40px;">mdi-arrow-left-bold</v-icon></span>
      </a>

      <a id="siguiente" href="#" aria-label="Next" v-show="pag * NUM_RESULTS / coches.length < 1"
        @click.prevent="pag += 1">
        <span aria-hidden="true"><v-icon class="flechas" style="font-size: 40px;">mdi-arrow-right-bold</v-icon></span>
      </a>

    </nav>
    <v-row v-if="coches.length">
      <v-col cols="8" sm="6" v-for="(coche, index) in coches" :key="coche.id"
        v-show="(pag - 1) * NUM_RESULTS <= index && pag * NUM_RESULTS > index">
        <app-carta :idCoche="coche.id" :imgCoche="coche.ruta_img" :modeloCoche="coche.modelo" :colorCoche="coche.color"
          :matriculaCoche="coche.matricula" :motorCoche="coche.motor" :marcaCoche="coche.marca_id"
          :esPropietario="coche.usuario_id" :marcas="marcas" />
      </v-col>
    </v-row>
    <div v-else id="busquedaFallida">
      <h1> No hay coches disponibles de la marca " {{ selected }} " {{ selectedEstado }} por el momento</h1>
      <div><img src="/../losentimos.png" alt=""><img src="/../coche.png" alt=""></div>
      <h4>Lo sentimos, intentelo de nuevo mas tarde.</h4>
    </div>
  </div>
</template>
<script>
import axios from "axios";
import AppBoton from "~/components/AppBoton.vue";
import AppCarta from "~/components/AppCarta.vue";

export default {
  asyncData({ params }) {
    return axios.get("http://127.0.0.1:8000/api/coches").then((response) => {
      return {
        coches: response.data.coches,
        cochesAux: response.data.coches,
        marcas: response.data.marcas,
        usuarios: response.data.usuarios,
      };
    });
  },
  components: {
    AppBoton,
    AppCarta,
  },
  data() {
    return {
      NUM_RESULTS: 2, // Numero de resultados por página
      pag: 1, // Página inicial
      user: {},
      coches: [],
      cochesAux: [],
      marcas: {},
      selected: null,
      selectedEstado: null,
      estadosCoche: [
        'Todos', 'En Venta', 'Comprado'
      ]
    };
  },

  methods: {
    async filtrar() {
      if (this.selected == "Todas") {
        axios.get("http://127.0.0.1:8000/api/coches").then(response => {
          this.coches = response.data.coches,
            this.marcas = response.data.marcas,
            this.usuarios = response.data.usuarios
        })
      } else {
        axios.get("http://127.0.0.1:8000/api/filtrar", { params: { nombre: this.selected } }).then(response => {
          this.coches = response.data.coches,
            this.marcas = response.data.marcas
          this.marcas.unshift({ "id": "-1", "nombre": "Todas" });
        })
      }
    },

    async filtrarPorEstado() {
      if (this.selectedEstado == 'En Venta') {
        this.selectedEstado = null;
        axios.get("http://127.0.0.1:8000/api/filtrarEstado", { params: { usuario_id: this.selectedEstado } }).then(response => {
          this.coches = response.data;
          this.selectedEstado = 'En Venta';

        })
      } else if (this.selectedEstado == 'Comprado') {
        this.selectedEstado = 1;
        axios.get("http://127.0.0.1:8000/api/filtrarEstado", { params: { usuario_id: this.selectedEstado } }).then(response => {
          this.coches = response.data;
          this.selectedEstado = 'Comprado';

        })
      } else {
        axios.get("http://127.0.0.1:8000/api/coches").then(response => {
          this.coches = response.data.coches,
            this.marcas = response.data.marcas,
            this.usuarios = response.data.usuarios
        })
      }

    },

    async filtroCompleto() {
      // SI ESTAN LOS DOS ( ok )
      // SI ESTA SOLO EL SELECT DE MARCA ( ok )
      // SI ESTA SOLO EL SELECT DE ESTADO ( ok )
      // SI NO HAY NINGUNO PQ TE VEO VENIR GONZALO QUE ME LO VAS A HACER PROBAR
      if (this.selected != null && this.selectedEstado == null || this.selectedEstado == 'Todos') {
        if (this.selected == "Todas") {
          axios.get("http://127.0.0.1:8000/api/coches").then(response => {
            this.coches = response.data.coches,
              this.marcas = response.data.marcas,
              this.usuarios = response.data.usuarios
          })
        } else {
          axios.get("http://127.0.0.1:8000/api/filtrar", { params: { nombre: this.selected } }).then(response => {
            this.coches = response.data.coches,
              this.marcas = response.data.marcas
            this.marcas.unshift({ "id": "-1", "nombre": "Todas" });
          })
        }


      } else if (this.selected == null && this.selectedEstado != null || this.selectedEstado == 'Todos') {
        if (this.selectedEstado == 'En Venta') {
          this.selectedEstado = null;
          axios.get("http://127.0.0.1:8000/api/filtrarEstado", { params: { usuario_id: this.selectedEstado } }).then(response => {
            this.coches = response.data;
            this.selectedEstado = 'En Venta';

          })
        } else if (this.selectedEstado == 'Comprado') {
          this.selectedEstado = 1;
          axios.get("http://127.0.0.1:8000/api/filtrarEstado", { params: { usuario_id: this.selectedEstado } }).then(response => {
            this.coches = response.data;
            this.selectedEstado = 'Comprado';

          })
        } else {
          axios.get("http://127.0.0.1:8000/api/coches").then(response => {
            this.coches = response.data.coches,
              this.marcas = response.data.marcas,
              this.usuarios = response.data.usuarios
          })
        }
      } else if (this.selectedEstado != null && this.selected != null) {
        if (this.selectedEstado == 'En Venta') {
          this.selectedEstado = null;
          axios.get("http://127.0.0.1:8000/api/filtroCompleto", { params: { nombre: this.selected } }).then(response => {
            this.coches = response.data.coches,
              this.marcas = response.data.marcas
            this.marcas.unshift({ "id": "-1", "nombre": "Todas" });
          })
          this.selectedEstado = 'En venta';
        } else {
          this.selectedEstado = 1;
          axios.get("http://127.0.0.1:8000/api/filtroCompleto", { params: { nombre: this.selected, usuario_id: this.selectedEstado } }).then(response => {
            this.coches = response.data.coches,
              this.marcas = response.data.marcas,
              this.marcas.unshift({ "id": "-1", "nombre": "Todas" });
          })
          this.selectedEstado = 'Comprado';
        }
      } else {
        axios.get("http://127.0.0.1:8000/api/coches").then(response => {
          this.coches = response.data.coches,
            this.marcas = response.data.marcas,
            this.usuarios = response.data.usuarios
        })
      }
    },

    escondido(e) {
      if (this.pag == 1) {
        return true;
      } else {
        return false;
      }
    }
  },

  mounted() {
    try {
      this.user = JSON.parse(sessionStorage.getItem("usuario"));
    } catch (error) {
      console.log(error);
    }
  },
};
</script>
<style>
@import url('https://fonts.googleapis.com/css2?family=Playfair+Display+SC&display=swap');

img {
  object-fit: cover;
}

#paginacionCoches {
  padding: 30px;
  padding-top: 0;
}

#filtro {
  display: flex;
  justify-content: center;
}

#filtro>div {
  width: 30% !important;
  margin-right: 30px;
}

main>div:first-of-type>div {
  background-repeat: no-repeat;
  background-size: cover;
  margin: 10px;

}

main>div>div>div>div>div:hover {
  transition: transform 0.5s ease;
}

main>div>div>div>div>div:hover {
  transform: scale(103%);
}

#tarjeta>div:first-of-type {
  display: flex;
  flex-direction: column;
  justify-content: center;

  font-size: 14px;
}

#tarjeta>div:first-of-type>div {
  text-align: center;
}

#filtro {
  display: flex;
}

#filtro>button {
  margin: 25px;
}

#contenedor {
  width: 99%;
  height: 100%;
}

h3,
#filtro>button>span {
  font-family: 'Playfair Display SC', serif !important;
}

#busquedaFallida {
  display: flex;
  justify-content: center;
  align-items: center;
  flex-direction: column;
}

#busquedaFallida>h1,
#busquedaFallida>h4 {
  margin: 20px;
}

#anterior {
  position: absolute;
  top: 88%;
  left:calc(47% + 10px);
  text-decoration: none;
}

#siguiente {
  position: absolute;
  top: 88%;
  left: calc(50% + 10px);;
  text-decoration: none;
}

</style>
