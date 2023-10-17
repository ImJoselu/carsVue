<template>
  <div id="fondo" v-if="this.usuarioSesion && this.usuarioSesion.esAdmin == 1">
    <h1>Añadir nueva noticia</h1>
    <form>
      <div class="form-field">
        <label for="titulo">Titulo</label>
        <input type="text" name="titulo" id="titulo">
        <p id="errorTitulo">Inserta un titulo valido, revisa la longitud de caracteres</p>
      </div>
      <div class="form-field">
        <label for="mensaje">Mensaje</label>
        <textarea name="mensaje" id="mensaje" cols="30" rows="10"></textarea>
        <p id="errorMensaje">Inserta un mensaje valido, revisa la longitud de caracteres</p>
      </div>
      <div class="form-field escondido">
        <label for="mensaje">id</label>
        <input type="number" name="id" id="id" :value="this.usuarioSesion.id">
      </div>
      <input type="submit" id="enviarForm" value="Añadir" @click="blogAdd" />
    </form>
  </div>
  <div v-else>
    <div class="mt-7 max-w-sm mx-auto textcenter card">
      <h1>Error 404</h1>
      <a href="/">Back to home</a>
    </div>
  </div>
</template>
<script>
import axios from "axios";

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
      console.log(error);
    };
  },

  methods: {
    blogAdd(e) {

      let titulo = document.getElementById('titulo').value;
      let mensaje = document.getElementById('mensaje').value;
      let id = document.getElementById('id').value;

      let tituloInput = document.getElementById('titulo');
      let mensajeInput = document.getElementById('mensaje');

      let errorTitulo = document.getElementById('errorTitulo');
      let errorMensaje = document.getElementById('errorMensaje');

      let validTitulo = false;
      let validMensaje = false;

      const RegEx = {
        titulo: /^[a-zA-ZáéíóúÁÉÍÓÚ]{1}[a-zA-Z0-9ÁÉÍÓÚáéíóú\s-]{2,25}$/,
        mensaje: /^[a-zA-ZáéíóúÁÉÍÓÚ]{1}[a-zA-Z0-9ÁÉÍÓÚáéíóú\s-]{2,255}$/,
      }

      if (RegEx.titulo.test(titulo)) {
        validTitulo = true;
        valido(errorTitulo, tituloInput);
      } else {
        validTitulo = false;
        noValido(errorTitulo, tituloInput)
      }

      if (RegEx.mensaje.test(mensaje)) {
        validMensaje = true;
        valido(errorMensaje, mensajeInput);
      } else {
        validMensaje = false;
        noValido(errorMensaje, mensajeInput)
      }

      if (validTitulo == true && validMensaje == true) {
        let formData = new FormData();
        formData.append('titulo', titulo);
        formData.append('mensaje', mensaje);
        formData.append('id', id);
        e.preventDefault();
        axios({
          method: 'post',
          url: 'http://127.0.0.1:8000/api/blog',
          data: formData,
          headers: { Authorization: "Bearer " + this.usuarioSesion.token }
        }).then(function (response) {
          console.log('success' + response);
          window.location = '/blog/listarBlog';
        }).catch(function (response) {
          console.log('error' + response);
        })
      } else {
        console.log('titulo' + validTitulo + ", mesnajej" + validMensaje);
      }

      function valido(error, input) {
        error.style.display = 'none';
        input.style.borderColor = "green";
      }
      function noValido(error, input) {
        error.style.display = 'block';
        input.style.borderColor = "red";
      }
    }
  },
}
</script>
<style scoped>
@import url("https://fonts.googleapis.com/css2?family=Montserrat+Alternates&display=swap");
@import url("https://fonts.googleapis.com/css2?family=Montserrat+Alternates&family=Slabo+27px&display=swap");
@import url('https://fonts.googleapis.com/css2?family=Racing+Sans+One&display=swap');

* {
  margin: 0;
  padding: 0;
}

#fondo {
  display: flex;
  flex-direction: column;
  text-align: center;
}

main {
  margin: 0 !important;
  padding: 0;
}

h1 {
  font-family: 'Racing Sans One', cursive;
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

}

form>div {
  display: flex;
  flex-direction: column;
  width: 30%;
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
input[type="number"],
#mensaje {
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
textarea #marca:focus {
  outline: none;
  border-color: #0078ff;
  box-shadow: 0 0 10px #0078ff;
}

#mensaje {
  height: 138px;
}

#enviarForm {
  margin: 10px;
  background-color: #0078ff;
  width: 10%;
  color: white;
  border: none;
  border-radius: 10px;
  cursor: pointer;
  padding: 10px 20px;
  font-size: 16px;
}

.escondido {
  position: absolute;
  visibility: hidden;
}
</style>
