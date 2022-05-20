<template>
  <div
    class="v-workspace"
    :style="{
      width: width + 'px',
      height: height + 'px',
    }"
  >
    <VCanvas :target-width="width" :target-height="height">
      <slot></slot>
    </VCanvas>
  </div>
</template>

<script>
import VCanvas from "./VCanvas.vue";

export default {
  components: {
    VCanvas,
  },
  props: {
    width: Number,
    height: Number,
  },
};
</script>

<style scoped>
.v-workspace {
  position: absolute;
  overflow: scroll;
}
</style>