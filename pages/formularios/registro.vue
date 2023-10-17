<template lang="">
  <div class="register-form">
    <h1>Registro</h1>
    <form action="http://127.0.0.1:8000/api/usuarios" method="post" enctype="multipart/form-data">
      <div class="form-field">
        <label for="nombre">Nombre:</label>
        <input type="text" id="nombre">
        <p id="nombreError">Ingresa un nombre válido</p>
        <label for="edad">Edad:</label>
        <input type="number" id="edad">
        <p id="edadError">Ingresa una edad válida</p>
        <label for="nif">NIF:</label>
        <input type="text" id="nif" placeholder="11111111A" >
        <p id="nifError">Ingresa un NIF válido</p>
        <label for="email">Correo Electronico:</label>
        <input type="email" id="email" placeholder="ejemplo@gmail.com">
        <p id="emailError">Ingresa un correo válido</p>
        <label for="password">Contraseña:</label>
        <input type="password" id="password">
        <p id="passError">Ingresa una contraseña válida</p>
        <label for="repassword">Repita la Contraseña:</label>
        <input type="password" id="repassword">
        <p id="repassError">Las contraseñas deben coincidir</p>
        <label for="imagen" class="form-label">Imagen</label>
        <input type="file" class="form-control" name="imagen" id="imagen" accept="image/*" @change="fileCambia">
        <p>Ingresa una imagen válida</p>
        <!--
        <label for="repassword">Repita la Password:</label>
        <input type="password" id="repassword" v-model="repassword">
        <p>Ingresa una contraseña válida</p>-->
      </div>
      <input type="submit" id="enviarForm" @click="registro"/>
    </form>
  </div>
</template>
<script>
import axios from "axios";

export default {
  methods: {
    registro(e) {
      e.preventDefault();
      let nombre = document.getElementById("nombre").value;
      let edad = document.getElementById("edad").value;
      let nif = document.getElementById("nif").value;
      let email = document.getElementById("email").value;
      let password = document.getElementById("password").value;
      let repassword = document.getElementById("repassword").value;

      let nombreInput = document.getElementById("nombre");
      let edadInput = document.getElementById("edad");
      let nifInput = document.getElementById("nif");
      let emailInput = document.getElementById("email");
      let passwordInput = document.getElementById("password");
      let repasswordInput = document.getElementById("repassword");

      let nombreError = document.getElementById("nombreError");
      let edadError = document.getElementById("edadError");
      let nifError = document.getElementById("nifError");
      let emailError = document.getElementById("emailError");
      let passError = document.getElementById("passError");
      let repasError = document.getElementById("repassError");

      let img = this.imgSeleccionada;

      let validNombre = false;
      let validEdad = false;
      let validNif = false;
      let validEmail = false;
      let validPass = false;

      const regEx = {
        datosPersonales: /^[a-zA-ZáéíóúÁÉÍÓÚ]{1}[a-zA-Z0-9ÁÉÍÓÚáéíóú\s-]{2,30}$/,
        nif: /^\d{8}[A-Za-z]$/,
        validarMail: /^[a-z0-9!#$%&'+/=?^_`{|}~-]+(?:.[a-z0-9!#$%&'+/=?^_`{|}~-]+)@(?:[a-z0-9](?:[a-z0-9-][a-z0-9])?.)+[a-z0-9](?:[a-z0-9-]*[a-z0-9])?$/,
        validarPass: /^([a-zA-Z0-9]{3,}$)/
      }

      if (regEx.datosPersonales.test(nombre)) {
        validNombre = true;
        valido(nombreError, nombreInput);
      } else {
        validNombre = false;
        noValido(nombreError, nombreInput);
      }

      if (regEx.nif.test(nif)) {
        validNif = true;
        valido(nifError, nifInput);
      } else {
        validNif = false;
        noValido(nifError, nifInput);
      }

      if (edad >= 18) {
        validEdad = true;
        valido(edadError, edadInput);
      } else {
        validEdad = false;
        noValido(edadError, edadInput);
      }

      if (regEx.validarMail.test(email)) {
        validEmail = true;
        valido(emailError, emailInput);
      } else {
        validEmail = false;
        noValido(emailError, emailInput);
      }

      if (regEx.validarPass.test(password)) {
        valido(passError, passwordInput);
        if (password === repassword) {
          validPass = true;
          valido(repasError, repasswordInput);
        } else {
          validPass = false;
          noValido(repassError, repasswordInput);
        }
      } else {
        validPass = false;
        noValido(passError, passwordInput);
      }

      if (validNombre && validEdad && validNif && validEmail && validPass) {
        let formData = new FormData();
        /* Validaciones */
        formData.append("nombre", nombre);
        formData.append("edad", edad);
        formData.append("nif", nif);
        formData.append("correo-electronico", email);
        formData.append("password", password);
        formData.append("imagen", img);

        axios
          .post("http://127.0.0.1:8000/api/usuarios", formData)
          .then((response) => {
            if (response.status == 200) {
              let usuarioSesion = JSON.stringify(response.data);
              sessionStorage.setItem("usuario", usuarioSesion);
              this.$router.push("/", () => {
                window.location.reload();
              });
            }
          })
          .catch((error) => {
            console.log(error);
          });
      } else {
        console.log("Nombre: " + validNombre + " Edad: " + validEdad + " Nif: " + validNif + " Email: " + validEmail + " Pass: " + validPass);
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
    }
  },
  data: () => ({
    imgSeleccionada: null,
  })
};
</script>
<style>
@import url("https://fonts.googleapis.com/css2?family=Montserrat+Alternates&display=swap");
@import url("https://fonts.googleapis.com/css2?family=Montserrat+Alternates&family=Slabo+27px&display=swap");

.register-form {
  display: flex;
  flex-direction: column;
  align-content: center;
  align-items: center;
  justify-content: center;

  background-image: url("/../fondo.jpg");
  background-repeat: no-repeat;
  background-size: cover;
  height: 100%;
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
