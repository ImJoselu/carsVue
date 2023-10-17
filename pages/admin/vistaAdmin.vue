<template>
  <div id="contenedor" v-if="this.usuarioSesion.esAdmin == 1">
    <div>
      <h1>Estas en la herramienta de <span>administracion</span></h1>
    </div>
    <div id="contenedorTarjetas">
      <v-card>
        <v-img src="/../fondoMarcas.jpg" aspect-ratio="2.75" class="imagenCard"></v-img>

        <v-card-title primary-title>
          <div>
            <h3 class="headline mb-0">Coches</h3>
          </div>
        </v-card-title>

        <v-card-actions>
          <v-btn @click="listadoCoches()">Lista de coches</v-btn>
          <v-btn @click="anyadirCoche()">Añadir un coche</v-btn>
        </v-card-actions>
      </v-card>


      <v-card>
        <v-img src="/../fondoUsers.jpg" aspect-ratio="2.75" class="imagenCard"></v-img>

        <v-card-title primary-title>
          <div>
            <h3 class="headline mb-0">Usuarios</h3>
          </div>
        </v-card-title>

        <v-card-actions>
          <v-btn @click="listaUsers()">Lista de Usuarios</v-btn>
          <v-btn @click="listaUsersDeleted()">Lista de Eliminados</v-btn>
        </v-card-actions>
      </v-card>

      <v-card>
        <v-img src="/../fondoMArcass.jpg" aspect-ratio="2.75" class="imagenCard"></v-img>

        <v-card-title primary-title>
          <div>
            <h3 class="headline mb-0">Marcas</h3>
          </div>
        </v-card-title>

        <v-card-actions>
          <v-btn @click="listaMarcas()">Lista de Marcas</v-btn>
          <v-btn @click="anyadirMarca()">Añadir una Marca</v-btn>

        </v-card-actions>
      </v-card>
    </div>
  </div>
  <div v-else>
    <div class="mt-7 max-w-sm mx-auto textcenter card">
      <h1>Error 404</h1>
      <a href="/">Back to home</a>
    </div>
  </div>
</template>
<script>
export default {
  data() {
    return {
      usuarioSesion: {},
    };
  },

  mounted() {
    try {
      this.usuarioSesion = JSON.parse(sessionStorage.getItem("usuario"));
    } catch (error) {
      console.log("usuario no existente");
      console.log(error);
    }
  },
  methods: {
    anyadirCoche() {
      this.$router.push("/coches/newCoche");
    },

    listadoCoches() {
      this.$router.push("/admin/listaCoches");
    },

    anyadirMarca() {
      this.$router.push("/marcas/newMarca");
    },

    listaMarcas() {
      this.$router.push("/admin/listaMarcas");
    },

    listaUsers() {
      this.$router.push("/admin/listaUsuarios");
    },

    listaUsersDeleted() {
      this.$router.push("/admin/listaUsuariosEliminados");
    },

    error() {
      this.$router.push("/404");

    }
  },
}
</script>
<style scoped>
#contenedor {
  height: 100%;
  width: 100%;

  display: flex;
  flex-direction: column;
  justify-content: space-around;

}

#contenedorTarjetas {
  margin: 200px;
  margin-top: 0;
  display: grid;
  grid-template-rows: 1fr;
  grid-template-columns: 1fr 1fr 1fr;

  gap: 20px;
}

.v-card__actions {
  display: flex;
  text-align: center;
  justify-content: space-around;
}

.v-btn {
  margin: 0;
  margin-bottom: 10px;
}

h1,
h4 {
  margin: 10px;
  text-align: center;
}

span {
  text-decoration: underline;
  color: rgb(255, 153, 0);
}

.imagenCard {
  height: 400px !important;
}
</style>
