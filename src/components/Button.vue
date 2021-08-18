<template>
  <button
    :style="{ background: color }"
    @click="$emit('btn-click')"
    class="btn"
  >
    {{ text }}
  </button>
</template>

<script>
export default {
  name: "Button",
  props: {
    color: String,
    text: String,
  },
  emits: ["btn-click"],
};
</script>

<style scoped></style>
