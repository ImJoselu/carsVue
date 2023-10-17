<template lang="">
  <div>
    <v-btn id="btnBlogAdd" :href="'/blog/add'">
      <v-icon>mdi-tooltip-plus-outline</v-icon>
    </v-btn>
  </div>
</template>
<script>
export default {

}
</script>
<style scoped>
#btnBlogAdd {
  height: 60px;
  width: 25px;
  position: fixed;
  z-index: 1;
  top: 85%;
  left: 93%;
  border-radius: 75%;
  color: rgb(0, 106, 255);
  border: 1px solid white;
}
#btnBlogAdd:hover {
  background-color: rgb(255, 238, 0);
}
</style>
