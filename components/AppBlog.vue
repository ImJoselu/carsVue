<template>
  <div id="btn">
      <v-btn id="btnBlog" :href="'/blog/listarBlog'">
        <v-icon>mdi-tooltip-outline</v-icon>
      </v-btn>
  </div>
</template>
<script>
</script>
<style scoped>

#btnBlog{
  margin: 10px;
}

#btnBlog.theme--dark {
  color: rgb(0, 106, 255);
  border: 1px solid white;

}
#btnBlog.theme--light {
  color: rgb(255, 147, 39) !important;
  border: 1px solid black;
}

#btnBlog:hover {
  background-color: rgb(255, 238, 0);
}

</style>
