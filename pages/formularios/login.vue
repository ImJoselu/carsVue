<template lang="">

  <div class="log-form">
    <h1>Log in</h1>
    <form>
      <p id="cuentaError">La cuenta introducida no existe.</p>
      <div class="form-field">
        <label for="email">Correo Electronico:</label>
        <input type="email" id="email" placeholder="ejemplo@gmail.com">
        <p id="emailError">Ingresa un correo electronico válido</p>
        <label for="password">Contraseña:</label>
        <input type="password" id="password">
        <p id="passError">Ingresa una contraseña válida, mas de tres carácteres</p>
      </div>
      <input type="submit" id="enviarForm" @click="login"/>
    </form>
  </div>
</template>
<script>
import axios from "axios";
export default {
  methods: {
    login(e) {
      e.preventDefault();
      let emailInput = document.getElementById("email");
      let passwordInput = document.getElementById("password");
      let email = document.getElementById("email").value;
      let password = document.getElementById("password").value;
      let emailError = document.getElementById("emailError");
      let passError = document.getElementById("passError");
      let cuentaError = document.getElementById("cuentaError");


      let validEmail = false;
      let validPass = false;

      const regEx = {
        validarMail: /^[a-z0-9!#$%&'+/=?^_`{|}~-]+(?:.[a-z0-9!#$%&'+/=?^_`{|}~-]+)@(?:[a-z0-9](?:[a-z0-9-][a-z0-9])?.)+[a-z0-9](?:[a-z0-9-]*[a-z0-9])?$/,
        validarPass: /^([a-zA-Z0-9]{3,}$)/
      }

      if (regEx.validarMail.test(email)) {
        validEmail = true;
        valido(emailError, emailInput);
      } else {
        validEmail = false;
        noValido(emailError, emailInput);
      }

      if (regEx.validarPass.test(password)) {
        validPass = true;
        valido(passError, passwordInput);
      } else {
        validPass = false;
        noValido(passError, passwordInput);
      }

      if (validEmail && validPass) {
        let formData = new FormData();
        formData.append("correo-electronico", email);
        formData.append("password", password);

        axios
          .post("http://127.0.0.1:8000/api/login", formData)
          .then((response) => {
            if (response.status == 200) {
              let usuarioSesion = JSON.stringify(response.data[0]);
              sessionStorage.setItem("usuario", usuarioSesion);
              this.$router.push("/", () => {
                window.location.reload();
              });
            }
          })
          .catch((response) => {
            cuentaError.style.display = "block"
          })
      } else {
        console.log("Email: " + validEmail + " Pass: " + validPass);
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
<style>
@import url("https://fonts.googleapis.com/css2?family=Montserrat+Alternates&display=swap");
@import url("https://fonts.googleapis.com/css2?family=Montserrat+Alternates&family=Slabo+27px&display=swap");

.log-form {
  display: flex;
  flex-direction: column;
  align-content: center;
  align-items: center;
  justify-content: center;
  background-image: "../static/11378.jpg";
  height: 100%;

  background-image: url("/../fondoLogin.jpg");
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
  padding-top: 10px;
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

form>div>p , form>p{
  display: none;
  color: rgb(255, 65, 65);
  text-decoration: underline;
}

#cuentaError {
  font-size: 20px;

}

input[type="email"],
input[type="password"] {
  color: white;
  padding: 10px;
  border: 1px solid #ccc;
  border-radius: 4px;
  box-sizing: border-box;
  width: 100%;
  margin-bottom: 10px;
}

input[type="email"]:focus,
input[type="password"]:focus {
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
