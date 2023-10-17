<template lang="">
  <div>
    <figure>
      <img src="/fondoBlog.jpg" alt="">
      <figcaption>Ultimas novedades de <span>cars</span></figcaption>
    </figure>
    <div id="contenedor">
      <template v-for="blog in blogs">
        <v-card class="mx-auto" color="#26c6da" max-width="400" v-if="blog.tipo == 1">
          <v-card-title>
            <v-icon large left > mdi-tooltip-outline </v-icon>
            <span class="title font-weight-light">{{ blog.titulo }}</span>
           </v-card-title>
          <v-card-text class="headline font-weight-bold">{{ blog.mensaje }}</v-card-text>
          <div id="footerTarjeta">
            <template v-for="usuario in usuarios">
             <span class="toc" v-if="blog.usuario_id == usuario.id"> Autor: {{ usuario.nombre }}</span>
           </template>
            <v-card-actions class="toc">
             <span  id="ajustesDisplay"> Fecha: {{ blog.publicada }}</span>
              <v-layout align-center justify-end>
                <v-icon class="mr-1">mdi-heart</v-icon>
                 <span class="subheading mr-2"> {{ blog.likes }} </span>
                 <span class="mr-1">·</span>
                <v-icon class="mr-1">mdi-share-variant</v-icon>
                 <span class="subheading">{{ blog.share }}</span>
              </v-layout>
            </v-card-actions>
          </div>
        </v-card v-if="blog.tipo == 1">
        <v-card class="mx-auto" color="#26C608" max-width="400" v-if="blog.tipo == 2" >
          <v-card-title>
            <v-icon large left > mdi-tooltip-outline </v-icon>
            <span class="title font-weight-light">{{ blog.titulo }}</span>
           </v-card-title>
          <v-card-text class="headline font-weight-bold">{{ blog.mensaje }}</v-card-text>
          <div id="footerTarjeta">
            <template v-for="usuario in usuarios">
             <span class="toc" v-if="blog.usuario_id == usuario.id"> Autor: {{ usuario.nombre }}</span>
           </template>
            <v-card-actions class="toc">
             <span  id="ajustesDisplay"> Fecha: {{ blog.publicada }}</span>
              <v-layout align-center justify-end>
                <v-icon class="mr-1">mdi-heart</v-icon>
                 <span class="subheading mr-2"> {{ blog.likes }} </span>
                 <span class="mr-1">·</span>
                <v-icon class="mr-1">mdi-share-variant</v-icon>
                 <span class="subheading">{{ blog.share }}</span>
              </v-layout>
            </v-card-actions>
          </div>
        </v-card>
        <v-card class="mx-auto" color="#b32821" max-width="400" v-if="blog.tipo == 3">
          <v-card-title>
            <v-icon large left > mdi-tooltip-outline </v-icon>
            <span class="title font-weight-light">{{ blog.titulo }}</span>
           </v-card-title>
          <v-card-text class="headline font-weight-bold">{{ blog.mensaje }}</v-card-text>
          <div id="footerTarjeta">
            <template v-for="usuario in usuarios">
             <span class="toc" v-if="blog.usuario_id == usuario.id"> Autor: {{ usuario.nombre }}</span>
           </template>
            <v-card-actions class="toc">
             <span  id="ajustesDisplay"> Fecha: {{ blog.publicada }}</span>
              <v-layout align-center justify-end>
                <v-icon class="mr-1">mdi-heart</v-icon>
                 <span class="subheading mr-2"> {{ blog.likes }} </span>
                 <span class="mr-1">·</span>
                <v-icon class="mr-1">mdi-share-variant</v-icon>
                 <span class="subheading">{{ blog.share }}</span>
              </v-layout>
            </v-card-actions>
          </div>
        </v-card>
      </template>
    </div>
    <AppBlogAdd v-if="this.usuarioSesion && this.usuarioSesion.esAdmin == 1"/>
  </div>
</template>
<script>
import axios from "axios";

export default {
  asyncData({ params }) {
    return axios.get("http://127.0.0.1:8000/api/blog").then((response) => {
      return {
        blogs: response.data.blog,
        usuarios: response.data.usuarios,
      };
    });
  },

  data() {
    return {
      usuarios: {},
      blogAux: {},
      blogs: {},
      usuarioSesion: {},
      nombreUser: '',
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
    randomLikes() {
      let cantidad = Math.random() * 1000;
      cantidad = cantidad.toFixed(0);

      return cantidad;
    }
  },
};
</script>
<style scoped>
@import url("https://fonts.googleapis.com/css2?family=Bebas+Neue&display=swap");

#contenedor {
  display: grid;
  grid-template-rows: auto;
  grid-template-columns: 1fr 1fr 1fr;

  row-gap: 40px;

}

img {
  object-fit: cover;
}

figure:first-of-type {
  text-align: center;
  font-size: 20px;
  font-family: "Bebas Neue", cursive;
}

figure:first-of-type>img {
  height: 100px;
  width: 100%;
}

figure:first-of-type>figcaption {
  position: absolute;
  top: 15px;
  left: 40%;
  font-size: 40px;
  color: white;
}

.toc {
  padding: 16px;
}

#contenedor {
  padding: 300px;
  padding-top: 0;
}

#contenedor>div {
  width: 100%;
  height: 250px;

  box-shadow: 3px 3px 1px 1px rgb(84, 84, 84);
}

#footerTarjeta {
  position: absolute;
  bottom: 0;
}

.ajustesLetra {
  font-size: 16px;
}

#ajustesDisplay {
  margin-right: 100px;
}
</style>
