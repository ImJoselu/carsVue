
<template>
  <v-btn class="ov-btn-slide-top botoness" :color="color" :href="href" :disabled="disabled">{{ value }}</v-btn>
</template>

<script>
export default {
  props: {
    value: {
      type: String,
      default: "Button"
    },
    color: {
      type: String,
      default: "warning"
    },
    href:{
      type: String,
    },
    disabled:{
      type: Boolean,
      default: false
    }
  }
}
</script>

<style>
  .botones {
    width: 189px;
    height: 18px;
    margin:3px;
  }
</style>

<!-- TIPOS -->

<!-- sucess -->
<!-- info -->
<!-- error -->
<!-- warning -->
