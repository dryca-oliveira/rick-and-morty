<template>
  <v-card color="transparent" width="620px" class="animation">
    <v-img src="../assets/portal3.png"></v-img>
  </v-card>
</template>

<script>
export default {
  data: () => ({}),
};
</script>
<style scoped>
.animation {
  animation: zoom 1.5s alternate;
  overflow: hidden;
}
@keyframes zoom {
  from {
    transform: rotate(0) scale(0, 0);
  }
  to {
    transform: rotate(360deg) scale(1, 1);
  }
}
.zoomOut-leave-active {
  transition: transform 1.5s;
}
.zoomOut-leave-to {
  transform: rotate(-360deg) scale(0, 0);
}
</style>