<template>
  <div id="fondo" class="log-form" v-if="this.usuarioSesion && this.usuarioSesion.esAdmin == 1">
    <h1>Añadir nuevo coche</h1>
    <form>
      <div class="form-field">
        <label for="modelo">Modelo del coche:</label>
        <input type="text" id="modelo">
        <p id="modeloError">Ingresa un coche válido</p>
      </div>
      <div class="form-field">
        <label for="color">Color del coche:</label>
        <input type="text" id="color">
        <p id="colorError">Ingresa un color válido</p>
      </div>
      <div class="form-field">
        <label for="matricula">Matricula del coche:</label>
        <input type="text" id="matricula" placeholder="1111AAA" style="text-transform:uppercase;" minlength="7"
          maxlength="7">
        <p id="matriculaError">Ingresa una matricula válida</p>
      </div>
      <div class="form-field">
        <label for="motor">Motor del coche:</label>
        <input type="text" id="motor" placeholder="Diesel">
        <p id="motorError">Ingresa un motor válido</p>
      </div>
      <div class="form-field">
        <label for="marca">Marca del coche</label>
        <!--
        <select v-model="marcaSeleccionada" class="form-select form-select-sm" id="marca" name="marca">
          <option v-for="marca in marcas" :key="marca.id" :value="marca.id">{{ marca.nombre }}</option>
        </select>
        -->
        <v-select v-model="marcaSeleccionada" :items="marcas" item-text="nombre" item-value="id" id="marca" name="marca">
        </v-select>
        <p id="marcaError">Selecciona una marca</p>
      </div>
      <div class="form-field">
        <label for="imagen" class="form-label">Imagen</label>
        <input type="file" class="form-control" name="imagen" id="imagen" accept="image/*" @change="fileCambia">
        <p>Ingresa una imagen válida</p>
      </div>
      <input type="submit" id="enviarForm" value="Añadir" @click="cocheAdd" />
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
  asyncData({ params }) {
    return axios.get("http://127.0.0.1:8000/api/marcas").then((response) => {
      return {
        marcas: response.data.marcas,
      };
    });
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
    cocheAdd(e) {
      e.preventDefault();
      let modelo = document.getElementById("modelo").value;
      let color = document.getElementById("color").value;
      let matricula = document.getElementById("matricula").value;
      let motor = document.getElementById("motor").value;
      let marca = this.marcaSeleccionada;
      let img = this.imgSeleccionada;

      let modeloInput = document.getElementById("modelo");
      let colorInput = document.getElementById("color");
      let matriculaInput = document.getElementById("matricula");
      let motorInput = document.getElementById("motor");
      let marcaInput = document.getElementById("marca");

      let modeloError = document.getElementById("modeloError");
      let colorError = document.getElementById("colorError");
      let matriculaError = document.getElementById("matriculaError");
      let motorError = document.getElementById("motorError");
      let marcaError = document.getElementById("marcaError");

      let modeloValid = false;
      let colorValid = false;
      let matriculaValid = false;
      let motorValid = false;
      let marcaValid = false;

      const regEx = {
        matricula: /^\d{4}[A-Za-z]{3}$/,
      }

      if (modelo == '') {
        modeloValid = false;
        noValido(modeloError, modeloInput);
      } else {
        modeloValid = true;
        valido(modeloError, modeloInput);
      }

      if (color == '') {
        colorValid = false;
        noValido(colorError, colorInput);
      } else {
        colorValid = true;
        valido(colorError, colorInput);
      }

      if (matricula == '' || !regEx.matricula.test(matricula)) {
        matriculaValid = false;
        noValido(matriculaError, matriculaInput);
      } else {
        matriculaValid = true;
        valido(matriculaError, matriculaInput);
      }

      if (motor == '') {
        motorValid = false;
        noValido(motorError, motorInput);
      } else {
        motorValid = true;
        valido(motorError, motorInput);
      }

      if (marca == null) {
        marcaValid = false;
        noValido(marcaError, marcaInput);
      } else {
        marcaValid = true;
        valido(marcaError, marcaInput);
      }

      if (modeloValid && colorValid && matriculaValid && motorValid && marcaValid) {

        let formData = new FormData();
        formData.append("modelo", modelo);
        this.model = modelo;
        formData.append("color", color);
        this.colorete = color;
        formData.append("matricula", matricula.toUpperCase());
        formData.append("motor", motor);
        formData.append("marca_id", marca);
        formData.append("imagen", img);

        axios({
          method: 'post',
          url: 'http://127.0.0.1:8000/api/coches',
          data: formData,
          headers: { Authorization: "Bearer " + this.usuarioSesion.token }
        }).then(res => {
          this.anyadirBlog();
        }).catch(function (response) {
          console.log('error' + response);
        })

      } else {
        console.log("Modelo: " + modeloValid + " Color: " + colorValid + " Matricula: " +
          matriculaValid + " Motor: " + motorValid + " Marca " + marcaValid);
      }

      function valido(error, input) {
        error.style.display = 'none';
        input.style.borderColor = "green";
      }
      function noValido(error, input) {
        error.style.display = 'block';
        input.style.borderColor = "red";
      }
    },

    fileCambia(input) {
      try {
        const files = input.target.files
        const fileReader = new FileReader()
        fileReader.addEventListener('load', () => {
          this.imageUrl = fileReader.result
        })
        fileReader.readAsDataURL(files[0])
        this.imgSeleccionada = files[0]
      } catch (error) {
        console.log(error);
      }
    },

    anyadirBlog: function () {
      let formData = new FormData();
      formData.append('titulo', 'Nuevo Coche a la venta');
      formData.append('mensaje', 'Un excelente ' + this.model + ", de color " + this.colorete);
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
    }
  },
  data: () => ({
    usuarioSesion: {},
    marcaSeleccionada: null,
    imgSeleccionada: null,
    model: '',
    colorete: '',
  })
};
</script>
<style scoped>
@import url("https://fonts.googleapis.com/css2?family=Montserrat+Alternates&display=swap");
@import url("https://fonts.googleapis.com/css2?family=Montserrat+Alternates&family=Slabo+27px&display=swap");
@import url('https://fonts.googleapis.com/css2?family=Racing+Sans+One&display=swap');

* {
  margin: 0;
  padding: 0;
}

main {
  margin: 0 !important;
  padding: 0;
}

h1 {
  font-family: 'Racing Sans One', cursive;
  color: #ffffff;
}

#fondo {
  width: 100%;
  height: 100%;
}

.log-form {
  display: flex;
  flex-direction: column;
  align-content: center;
  align-items: center;
  justify-content: center;
  background-image: "../static/11378.jpg";
  height: 100%;

  background-image: url("/../fondoMarcas.jpg");
  background-repeat: no-repeat;
  background-size: cover;
}

form {
  display: flex;
  flex-direction: column;
  border-radius: 30px;
  justify-content: center;
  align-items: center;
  margin-bottom: 30px;
  width: 30%;
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
input[type="file"],
#marca {
  color: white;
  padding: 10px;
  border: 1px solid #ccc;
  border-radius: 4px;
  box-sizing: border-box;
  width: 100%;
  margin-bottom: 10px;
}

input[type="text"]:focus,
input[type="file"]:focus,
#marca:focus {
  outline: none;
  border-color: #0078ff;
  box-shadow: 0 0 10px #0078ff;
}

#enviarForm {
  margin: 10px;
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
