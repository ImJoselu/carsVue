<template>
  <div>
    <v-card style="border: 2px solid;">
      <v-img :src="imgRuta" aspect-ratio="2.75"></v-img>
      <v-card-title primary-title id="tarjeta">
        <div>
          <div>
            <h3 class="headline mb-0">- {{ modeloCoche }} -</h3>
          </div>
          <p>Marca del coche: {{ marcaNombre }}</p>
          <p>Coche de color: {{ colorCoche }}</p>
          <p>Motor del coche: {{ motorCoche }}</p>
        </div>
        <v-card-actions id="botones">
          <!--DETALLES COCHE-->
          <AppBoton :href="'/coches/' + idCoche" value="Detalles" color="#008000" />
          <!---->
          <v-spacer></v-spacer>
          <!--EDITAR COCHE-->
          <AppBoton v-if="eresAdmin()" :href="'/coches/edit/' + idCoche" value="Editar" color="#FFA500"
            :disabled="cocheComprado()" />
          <!---->
          <v-spacer></v-spacer>
          <!--ELIMINAR COCHE-->
          <button v-if="eresAdmin() && !cocheComprado()" v-on:click="eliminarCoche(idCoche)" :disabled="cocheComprado()"
            id="btnEliminar" class="ov-btn-slide-top v-btn v-btn--is-elevated v-btn--has-bg v-size--default claseBotones">
            <span class="v-btn__content">Eliminar</span> </button>
          <AppBoton v-else-if="eresAdmin()" href="#" value="Eliminar" color="#1E90FF" :disabled="true" />
          <!---->
          <v-spacer></v-spacer>
          <!--COMPRAR COCHE-->
          <button v-if="!cocheComprado() && usuarioSesion" v-on:click="comprarCoche(idCoche)" id="btnComprar"
            class="ov-btn-slide-top v-btn v-btn--is-elevated v-btn--has-bg v-size--default claseBotones">
            <span class="v-btn__content">Comprar</span>
          </button>
          <AppBoton v-else-if="cocheComprado() && !posesion()" href="#" value="Comprado" color="#1E90FF"
            :disabled="true" />
          <button v-else-if="cocheComprado() && posesion()" v-on:click="venderCoche(idCoche)" id="btnComprar"
            class="ov-btn-slide-top v-btn v-btn--is-elevated v-btn--has-bg v-size--default claseBotones">
            <span class="v-btn__content">Vender</span>
          </button>
          <AppBoton v-else :href="'/formularios/login'" value="Comprar" color="#1E90FF" />
          <!---->
        </v-card-actions>
      </v-card-title>
    </v-card>
  </div>
</template>
<script>
import AppBoton from "../components/AppBoton.vue";
import axios from "axios";

export default {
  props: {
    idCoche: {},
    imgCoche: {},
    modeloCoche: {},
    colorCoche: {},
    matriculaCoche: {},
    motorCoche: {},
    esPropietario: {},
    marcaCoche: {},
    marcas: {},
  },
  data() {
    return {
      marcaNombre: "",
      usuarioSesion: {},
    };
  },

  computed: {
    imgRuta() {
      return "http://127.0.0.1:8000" + this.imgCoche;
    },
  },

  mounted() {
    try {
      this.usuarioSesion = JSON.parse(sessionStorage.getItem("usuario"));
      this.marcas.forEach((marca) => {
        if (marca.id == this.marcaCoche) {
          this.marcaNombre = marca.nombre;
        }
      });
    } catch (error) {
      console.log("usuario no existente");
      console.log(error);
    }
  },

  methods: {
    eresAdmin() {
      if (this.usuarioSesion) {
        if (this.usuarioSesion.esAdmin) {
          return true;
        } else {
          return false;
        }
      } else {
        return false;

      }
    },
    cocheComprado() {
      if (this.esPropietario) {
        return true;
      } else {
        return false;
      }
    },

    posesion() {
      if (this.usuarioSesion) {
        if (this.esPropietario == this.usuarioSesion.id) {
          return true;
        } else {
          return false;
        }
      }
    },

    venderCoche(idCoche) {
      console.log("hello");
      // CommonJS
      const Swal = require('sweetalert2')
      Swal.fire({
        title: '¿Quieres vender este coche?',
        text: "Estas apunto de vender tu coche",
        icon: 'info',
        showCancelButton: true,
        confirmButtonColor: '#3085d6',
        cancelButtonColor: '#d33',
        confirmButtonText: 'Vender',
        cancelButtonText: 'Salir'
      }).then((result) => {
        if (result.isConfirmed) {
          let titulo = "Han puesto a la venta un coche!";
          let mensaje = "Modelo: " + this.modeloCoche + "    -    Color: " + this.colorCoche;
          let tipo = 2;
          this.anyadirBlog(titulo, mensaje , tipo);
          axios({
            method: 'post',
            url: 'http://127.0.0.1:8000/api/coches/' + idCoche + '/vender',
            headers: { Authorization: "Bearer " + this.usuarioSesion.token }
          })
            .then((response) => {
              if (response.status == 200) {
                Swal.fire({
                  tittle: '¡Venta Realizada!',
                  text: "Muchas gracias por su venta.",
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
    },

    comprarCoche(idCoche) {
      // CommonJS
      const Swal = require('sweetalert2')
      Swal.fire({
        title: '¿Quieres comprar este coche?',
        text: "Estas apunto de comprar un coche",
        icon: 'info',
        showCancelButton: true,
        confirmButtonColor: '#3085d6',
        cancelButtonColor: '#d33',
        confirmButtonText: 'Comprar',
        cancelButtonText: 'Salir'
      }).then((result) => {
        if (result.isConfirmed) {
          let titulo = "Alguien ha comprado un coche";
          let mensaje = "Modelo: " + this.modeloCoche + "    -    Color: " + this.colorCoche;
          let tipo = 1;
          this.anyadirBlog(titulo, mensaje , tipo);
          let mensajeFactura = "USTED HA COMPRADO UN COCHE - " + mensaje;
          this.factura('Factura', mensajeFactura);
          axios({
            method: 'post',
            url: 'http://127.0.0.1:8000/api/coches/' + idCoche + '/comprar',
            headers: { Authorization: "Bearer " + this.usuarioSesion.token }
          })
            .then((response) => {
              if (response.status == 200) {

                Swal.fire({
                  tittle: '¡Compra Realizada!',
                  text: "Muchas gracias por tu compra.",
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
    },
    eliminarCoche(idCoche) {
      const Swal = require('sweetalert2')
      Swal.fire({
        title: '¿Quieres eliminar este coche?',
        text: "Estas apunto de eliminar un coche",
        icon: 'warning',
        showCancelButton: true,
        confirmButtonColor: '#d33',
        cancelButtonColor: '#3085d6',
        confirmButtonText: 'Eliminar',
        cancelButtonText: 'No eliminar'
      }).then((result) => {
        if (result.isConfirmed) {
          let titulo = "Se ha eliminado un coche";
          let mensaje = "Modelo: " + this.modeloCoche + "    -    Color: " + this.colorCoche;
          let tipo = 3;
          this.anyadirBlog(titulo, mensaje , tipo);
          axios({
            method: 'delete',
            url: 'http://127.0.0.1:8000/api/coches/' + idCoche,
            headers: { Authorization: 'Bearer ' + this.usuarioSesion.token }
          })
            .then((response) => {
              if (response.status == 200) {
                Swal.fire({
                  tittle: '¡Coche Eliminado!',
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
    },
    anyadirBlog: function (titulo, mensaje , tipo) {
      let formData = new FormData();
      formData.append('titulo', titulo);
      formData.append('mensaje', mensaje);
      formData.append('tipo', tipo);
      formData.append('id', this.usuarioSesion.id);
      axios({
        method: 'post',
        url: 'http://127.0.0.1:8000/api/blog',
        data: formData,
        headers: { Authorization: "Bearer " + this.usuarioSesion.token }
      }).then(function (response) {
        console.log('success' + response);
        window.location = '/';
      }).catch(function (response) {
        console.log('error' + response);
      })
    },

    factura(filename, text) {
      var element = document.createElement('a');
      element.setAttribute('href', 'data:text/plain;charset=utf-8,' + encodeURIComponent(text));
      element.setAttribute('download', filename);

      element.style.display = 'none';
      document.body.appendChild(element);

      element.click();

      document.body.removeChild(element);
    }
  },
  components: { AppBoton },
};
</script>
<style>
@import url("https://fonts.googleapis.com/css2?family=Montserrat:wght@400;500;700&display=swap");

.swal2-container.swal2-backdrop-show,
.swal2-container.swal2-noanimation {
  background: rgba(0, 0, 0, .60) !important;
}

#botones {
  display: flex;
  flex-direction: column;
}

#botones>a,
#botones>button {
  margin: 3px;
  width: 200px;
  height: 40px;
}

#btnComprar {
  background-color: #1E90FF;
}

#btnEliminar {
  background-color: #ff1e1e;
}

#tarjeta {
  border-top: 3px solid black;
  display: flex;
  justify-content: space-around;
  background-color: #bf0000;

  font-family: "Montserrat", sans-serif;
  color: #ffffff;
}

#tarjeta>div:first-of-type {
  padding-right: 6%;
  border-right: 1px solid black;
}
</style>
