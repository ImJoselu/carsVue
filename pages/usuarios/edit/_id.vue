<template lang="">
  <div class="register-form" v-if="this.usuarioSesion && this.usuarioSesion.esAdmin == 1">
    <h1 v-if=usuario>Editando a {{ usuario.nombre }}</h1>
    <form onsubmit="return false">
      <div class="form-field">
        <label for="nombre">Nombre:</label>
        <input type="text" id="nombre" :value="usuario.nombre">
        <p>Ingresa un nombre válido</p>
        <label for="edad">Edad:</label>
        <input type="number" id="edad" :value="usuario.edad">
        <p>Ingresa una edad válida</p>
        <label for="nif">NIF:</label>
        <input type="text" id="nif" :value="usuario.nif">
        <p>Ingresa un NIF válido</p>
        <label for="email">Correo Electronico:</label>
        <input type="email" id="email" :value="usuario.correo_electronico">
        <p>Ingresa un correo electronico válido</p>
        <label for="password">Contraseña:</label>
        <input type="password" id="password" :value="usuario.password">
        <p>Ingresa una contraseña válida</p>


        <label for="imagen" class="form-label">Imagen</label>
        <input type="file" class="form-control" id="imagen" accept="image/*" @change="fileCambia">
        <p>Ingresa una imagen válida</p>

        <!--
        <label for="repassword">Repita la Password:</label>
        <input type="password" id="repassword" v-model="repassword">
        <p>Ingresa una contraseña válida</p>-->
      </div>
      <div id="capsula">
        <img :src="this.imgSeleccionada" alt="DSSADDSA" id="imagendsdds">
        <input type="submit" id="enviarForm" value="Actualizar" @click="actualizarUser()" />
      </div>
    </form>
  </div>
  <div v-else class="mt-7 max-w-sm mx-auto textcenter card">
    <h1>Error 404</h1>
    <a href="/">Back to home</a>
  </div>
</template>
<script>
import axios from "axios";

export default {
  data: function () {
    return {
      coches: [],
      usuario: {},
      marcas: [],
      imgSeleccionada: null,
    }
  },
  mounted() {
    let usuarioSesion = JSON.parse(sessionStorage.getItem('usuario')).token;
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
        this.imgSeleccionada = 'http://127.0.0.1:8000' + this.usuario.ruta_img;

      });
  },

  computed: {

    imgRuta() {
      return "http://127.0.0.1:8000" + this.usuario.ruta_img;
    },

  },

  methods: {
    actualizarUser(e) {
      let usuarioSesion = JSON.parse(sessionStorage.getItem('usuario')).token;
      let nombre = document.getElementById('nombre').value;
      let edad = document.getElementById('edad').value;
      let nif = document.getElementById('nif').value;
      let email = document.getElementById('email').value;
      let password = document.getElementById('password').value;
      let img = document.getElementById('imagen').files[0];

      let formData = new FormData();
      formData.append('nombre', nombre);
      formData.append('edad', edad);
      formData.append('nif', nif);
      formData.append('correo-electronico', email);
      formData.append('password', password);
      formData.append('imagen', img);
      formData.append('_method', 'put');


      axios({
        method: 'post',
        url: 'http://127.0.0.1:8000/api/usuarios/edit/' + this.$route.params.id,
        data: formData,
        headers: { Authorization: "Bearer " + usuarioSesion }
      })
        .then((response) => {
          if (response.status == 200) {
            this.$router.push("/")
          };
        })
        .catch((error) => {
          console.log(error);
        });
    },

    fileCambia(input) {
      try {
        const files = input.target.files
        const fileReader = new FileReader()
        fileReader.addEventListener('load', () => {
          this.imageUrl = fileReader.result
        })
        fileReader.readAsDataURL(files[0])
        fileReader.onload = (e) => {
          this.imgSeleccionada = e.target.result;
        }
        this.imagen = files[0];
      } catch (error) {
        console.log(error);
      }
    }
  }
}
</script>
<style scoped>
@import url("https://fonts.googleapis.com/css2?family=Montserrat+Alternates&display=swap");
@import url("https://fonts.googleapis.com/css2?family=Montserrat+Alternates&family=Slabo+27px&display=swap");

h1 h3 {
  font-family: "Montserrat", sans-serif;
}

#capsula {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  margin: 50px;
}

#imagendsdds {
  height: 400px;
  width: 700px;
  padding: 10px;
  margin: 20px;
  border: 1px solid #ccc;
  border-radius: 4px;
}

.register-form {
  display: flex;
  flex-direction: column;
  align-content: center;
  align-items: center;
  justify-content: center;


  height: 100%;
}

form {
  display: flex;
  flex-direction: row;

  border-radius: 30px;
  justify-content: center;
  align-items: center;
  margin-bottom: 30px;
  padding: 30px;
  width: 60%;
}

form>div {
  display: flex;
  flex-direction: column;
  width: 70%;
}

form>div>label {
  font-size: 25px;
  font-family: "Slabo 27px", serif;
}

form>div>p {
  display: none;
  color: rgb(255, 65, 65);
}

input[type="text"],
input[type="email"],
input[type="password"],
input[type="number"],
input[type="file"] {
  padding: 10px;
  border: 1px solid #ccc;
  border-radius: 4px;
  box-sizing: border-box;
  width: 100%;
  margin-bottom: 10px;
  background: transparent;
  color: white;
}


input[type="text"]:focus,
input[type="email"]:focus,
input[type="password"]:focus,
input[type="number"]:focus,
input[type="file"]:focus {
  outline: none;
  border-color: #0078ff;
  box-shadow: 0 0 10px #0078ff;
}


#enviarForm {
  background-color: #0078ff;
  width: 40%;
  color: white;
  border: none;
  border-radius: 10px;
  cursor: pointer;
  padding: 10px 20px;
  font-size: 16px;
}

#enviarForm:hover {
  background-color: #0055c3;
}
</style>
