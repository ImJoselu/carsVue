<template>
  <div id="fondo" class="log-form">
    <h1>Añadir nueva marca</h1>
    <form>
      <div class="form-field">
        <label for="marca">Nombre de la marca:</label>
        <input type="text" id="marca">
        <p id="marcaError">Ingresa una marca válida</p>
      </div>
      <input type="submit" id="enviarForm" value="Añadir" @click="marcaAdd" />
    </form>
  </div>
</template>
<script>
import axios from "axios";

export default {
  mounted() {
    try {
      this.usuarioSesion = JSON.parse(sessionStorage.getItem("usuario"));
    } catch (error) {
      console.log("usuario no existente");
      console.log(error);
    }
  },
  methods: {
    marcaAdd(e) {
      e.preventDefault();
      let marca = document.getElementById("marca").value;
      let marcaInput = document.getElementById("marca");
      let marcaError = document.getElementById('marcaError');

      let validMarca = false;

      const regEx = {
        marca: /^[a-zA-ZáéíóúÁÉÍÓÚ]{1}[a-zA-Z0-9ÁÉÍÓÚáéíóú\s-]{2,30}$/,
      }

      if (regEx.marca.test(marca)) {
        validMarca = true;
        valido(marcaError, marcaInput);
      } else {
        validMarca = false;
        noValido(marcaError, marcaInput);
      }

      if (validMarca) {
        let formData = new FormData();
        formData.append("nombre", marca);

        axios({
          method: 'post',
          url: 'http://127.0.0.1:8000/api/marcas',
          data: formData,
          headers: { Authorization: "Bearer " + this.usuarioSesion.token }
        }).then(function (response) {
          console.log('success' + response);
          window.location = '/';
        }).catch(function (response) {
          console.log('error' + response);
        })
      } else {
        console.log("Error en el nombre de la marca");
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
  },
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

  background-image: url("/../mcs.jpg");
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
  padding: 30px;
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

input[type="text"] {
  color: white;
  padding: 10px;
  border: 1px solid #ccc;
  border-radius: 4px;
  box-sizing: border-box;
  width: 100%;
  margin-bottom: 10px;
}

input[type="text"]:focus {
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

#enviarForm:hover {
  background-color: #0055c3;
}
</style>
