<template lang="">
  <div class="register-form" v-if="this.usuarioSesion && this.usuarioSesion.esAdmin == 1">
    <h1 v-if=coche>Editando el modelo: {{ coche.modelo }}</h1>
    <h3 v-if=coche>Con id: {{ coche.id }}</h3>
    <form onsubmit="return false">
      <div class="form-field">
        <label for="marca">Marca del coche:</label>
        <input type="text" id="nombre" :value="this.marcaSeleccionada" disabled>
        <label for="modelo">Modelo:</label>
        <input type="text" id="modelo" :value="coche.modelo">
        <p>Ingresa un modelo válido</p>
        <label for="color">Color:</label>
        <input type="text" id="color" :value="coche.color">
        <p>Ingresa un color válida</p>
        <label for="matricula">Matricula:</label>
        <input type="text" id="matricula" :value="coche.matricula">
        <p>Ingresa una matricula válido</p>
        <label for="motor">Motor:</label>
        <input type="text" id="motor" :value="coche.motor">
        <p>Ingresa un motor válido</p>
        <label for="imagen" class="form-label">Imagen</label>
        <input type="file" class="form-control" id="imagen" accept="image/*" @change="fileCambia">
        <p>Ingresa una imagen válida</p>

      </div>
      <div id="capsula">
        <img :src="this.imgSeleccionada" alt="DSSADDSA" id="imagendsdds">
        <input type="submit" id="enviarForm" value="Actualizar" @click="actualizarCoche()" />
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
      usuarioSesion: {},
      coche: {},
      marcas: [],
      imgSeleccionada: '',
      marcaSeleccionada: '',
      imagen: ''
    }
  },
  mounted() {
    this.usuarioSesion = JSON.parse(sessionStorage.getItem('usuario'));

    axios
      .get("http://127.0.0.1:8000/api/coches/" + this.$route.params.id, {
        headers: {
          Authorization: "Bearer " + this.usuarioSesion.token,
        },
      })
      .then((response) => {
        this.coche = response.data.coche,
          this.marcas = response.data.marcas,
          this.nombreMarca()
        this.imgSeleccionada = 'http://127.0.0.1:8000' + this.coche.ruta_img;
        this.imagen = 'http://127.0.0.1:8000' + this.coche.ruta_img;
      });
  },

  computed: {
    /*
        imgRuta() {
          return "http://127.0.0.1:8000" + this.usuario.ruta_img;
        },
    */
  },

  methods: {
    actualizarCoche() {

      let matricula = document.getElementById('matricula').value;
      let modelo = document.getElementById('modelo').value;
      let color = document.getElementById('color').value;
      let motor = document.getElementById('motor').value;
      let img = document.getElementById('imagen').files[0];

      let formData = new FormData();
      formData.append('modelo', modelo);
      formData.append('color', color);
      formData.append('matricula', matricula);
      formData.append('motor', motor);
      formData.append('imagen', img);
      formData.append('_method', 'put');

      axios({
        method: 'post',
        url: 'http://127.0.0.1:8000/api/coches/edit/' + this.$route.params.id,
        data: formData,
        headers: { Authorization: "Bearer " + this.usuarioSesion.token }
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
    nombreMarca() {
      console.log(this.marcas);
      this.marcas.forEach((marca) => {
        if (marca.id == this.coche.marca_id) {
          this.marcaSeleccionada = marca.nombre;
        }
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
  box-sizing: border-box;
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


#marca {
  background: black;
  color: white;
  border-radius: 8px;
  border: 1px solid #ccc;
  margin-bottom: 8px;
  font-size: 17px;
  height: 46px;
  padding: 5px;
}

#marca>option {
  height: 100px;
  border-bottom: 8px;
}

#enviarForm:hover {
  background-color: #0055c3;
}
</style>
