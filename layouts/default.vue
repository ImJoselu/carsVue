<template>
  <v-app>
    <v-icon aria-hidden="false"></v-icon>
    <v-app-bar :clipped-left="clipped" fixed app>
      <v-toolbar-title>
        <h3 v-if="this.usuarioSesion">Bienvenido, {{ usuarioSesion.nombre }}!</h3>
        <h3 v-else>Bienvenido</h3>
      </v-toolbar-title>
      <v-spacer></v-spacer>
    <!--
      <div v-if="comprobar()">
        <v-list class="nav-list d-flex justify-space-between" v-if="comprobarAdmin()">
          <v-list-item v-for="item in items" :key="item" class="nav-list-item" @click="navigate(item)">
            <v-list-item-content>
              <v-list-item-title style="font-size: 18px;">{{ item }}</v-list-item-title>
            </v-list-item-content>
          </v-list-item>
        </v-list>
      </div>
        -->
      <v-spacer></v-spacer>
      <AppBoton v-if="comprobarAdmin()" href="/admin/vistaAdmin" value="Administracion" color="success" style="margin-right: 10px" />
      <AppBoton href="/" value="Catálogo" color="warning" style="margin-right: 10px" />
      <div v-if="comprobar()">
        <AppBoton value="Mi Perfil" color="error" style="margin-right: 10px" @click.native="showPerfil()" />
        <AppBoton @click.native="logout()" value="Logout" color="info" style="margin-right: 10px" />
      </div>
      <div v-else>
        <AppBoton href="/formularios/login" value="Login" color="error" style="margin-right: 10px" />
        <AppBoton href="/formularios/registro" value="Registro" color="info" />
      </div>
      <v-spacer></v-spacer>
      <v-spacer></v-spacer>
      <app-blog />
      <app-tema />
      <app-atras />
    </v-app-bar>

    <v-main>
      <nuxt />
    </v-main>
    <v-footer>
      <span> Hecho por: Jose Luis Tortola Cervera</span>
    </v-footer>
  </v-app>
</template>
<script>
import AppBoton from "@/components/AppBoton.vue";
import axios from "axios";
import AppAtras from '../components/AppAtras.vue';
import AppBlog from '../components/AppBlog.vue';

export default {
  name: "App",
  components: {
    AppBoton,
    AppAtras,
    AppBlog,

  },
  data() {
    return {
      usuarioSesion: {},
      clipped: false,
      fixed: true,
      // items: [
      //  "Lista de Usuarios",
      //  "Usuarios Eliminados",
      //  "Añadir marca",
      //  "Añadir coche",
      // ],
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
    /* navigate(item) {
       switch (item) {
         case "Lista de Usuarios":
           this.$router.push("/admin/listaUsuarios");
           break;
         case "Usuarios Eliminados":
           this.$router.push("/admin/listaUsuariosEliminados");
           break;
         case "Añadir marca":
           this.$router.push("/marcas/newMarca");
           break;
         case "Añadir coche":
           this.$router.push("/coches/newCoche");
           break;
         default:
           this.$router.push("/");
       }
  }, */

    comprobar() {
      if (sessionStorage.getItem("usuario") == null) {
        return false;
      } else {
        return true;
      }
    },
    comprobarAdmin() {
      if (sessionStorage.getItem("usuario")) {
        if (this.usuarioSesion.esAdmin) {
          return true
        } else {
          return false;
        }
      } else {
        return false;
      }
    },
    logout() {

      axios
        .get("http://127.0.0.1:8000/api/logout", {
          headers: {
            Authorization: "Bearer " + this.usuarioSesion.token,
          },
        });

      sessionStorage.removeItem("usuario");
      this.$router.push("/formularios/login", () => {
        window.location.reload();
      });

    },

    showPerfil() {
      this.$router.push("/usuarios/" + this.usuarioSesion.id);
    },
  },
};
</script>
<style>
@import url('https://fonts.googleapis.com/css2?family=Playfair+Display+SC&display=swap');

::-webkit-scrollbar {display:none;}

* {
  margin: 0;
  padding: 0;

}


#enlace {
  text-decoration: none;
  color: white;
}

.nav-list {
  background-color: #f1f1f1;
  padding: 0;
  height: 50px;
  width: 80vh;
  text-align: center;
}

.nav-list-item {
  flex-grow: 1;
  text-align: center;
  cursor: pointer;
  background-color: transparent;
}

.nav-list-item:hover {
  background-color: #434343;
}

.nav-list-item .v-list-item__content {
  display: flex;
  align-items: center;
  justify-content: center;
  height: 100%;
}
h3 {
  font-family: 'Playfair Display SC', serif;
}
</style>
