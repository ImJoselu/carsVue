<template lang="">
  <div>
    <v-btn id="btnAtras" @click="retroceder()">
      <v-icon>mdi-arrow-left</v-icon>
    </v-btn>
  </div>
</template>
<script>
export default {
 methods: {
  retroceder(){
    window.history.back();
  }
 }
}
</script>
<style scoped>
#btnAtras {
  height: 53px;
  width: 25px;
  position: absolute;
  z-index: 0;
  top: 85px;
  left: 8%;
  border-radius: 75%;
}
</style>
