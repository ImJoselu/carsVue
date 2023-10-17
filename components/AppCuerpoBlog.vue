<template lang="">
  <div>
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
  </div>
</template>
<script>
export default {

}
</script>
<style lang="">

</style>
