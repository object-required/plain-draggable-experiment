<script setup>
</script>

<template>
  <div class="layout">
    <div class="layout__header">
      <h1>plain-draggable-experiment</h1>
    </div>
    <div class="layout__body">
      <VWorkspace id="workspace" :width="640" :height="480">
        <VBlock>foo</VBlock>
        <VBlock>bar</VBlock>
        <VBlock>baz</VBlock>
      </VWorkspace>
    </div>
  </div>
</template>

<script>
import VWorkspace from "./components/VWorkspace.vue";
import VBlock from "./components/VBlock.vue";

export default {
  components: {
    VWorkspace,
    VBlock,
  },
};
</script>

<style scoped>
.layout {
  height: -webkit-fill-available;
  display: flex;
  flex-direction: column;
}

.layout__header {
  background: lightblue;
}

.layout__body {
  display: flex;
  flex-direction: column;
  flex-grow: 1;
  background: lightgreen;
}

#workspace {
  flex-grow: 1;
  width: 100%;
  height: 400px;
}
</style>