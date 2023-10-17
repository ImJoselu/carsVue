<template>
  <div>
      <v-btn v-if="!$vuetify.theme.dark" @click="modoOscuro" id="day">
        <v-icon>mdi-weather-sunny</v-icon>
      </v-btn>
      <v-btn v-else @click="modoOscuro" id="night">
        <v-icon>mdi-weather-night</v-icon>
      </v-btn>
  </div>
</template>

<script>
export default {
  methods: {
    modoOscuro() {
          this.$vuetify.theme.dark = !this.$vuetify.theme.dark;

          if (!this.$vuetify.theme.dark) {
              localStorage.setItem('darkMode', 'enabled')
          } else {
              localStorage.setItem('darkMode', 'disabled')
          }
      },
  }
}
</script>

<style>
#night {
  color: rgb(0, 106, 255);
  text-shadow: 1px 1px 1px rgb(81, 0, 255);
  background: transparent;
  border: 1px solid rgb(255, 255, 255);


}

#day {
  color: rgb(255, 147, 39) !important;
  text-shadow: 1px 1px 1px rgb(255, 225, 0);
  background: transparent;
  border: 1px solid black;

}

#night:hover {
  background-color: rgb(255, 238, 0);
}

#day:hover {
  background-color: rgb(255, 238, 0);
}

</style>
