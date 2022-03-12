<template>
  <div
    class="cell"
    :style="{ backgroundColor: `hsl(200, 50%, ${setbgColor}%)` }"
  >
    <p v-if="value != 1">{{ value }}</p>
  </div>
</template>

<script>
export default {
  data() {
    return {
      backgroundColorValue: 90,
    };
  },
  computed: {
    setbgColor() {
      return parseInt(this.value) == 1 || isNaN(parseInt(this.value))
        ? 90
        : 90 - 30 * (Math.log2(parseInt(this.value)) / 11);
    },
  },
  props: {
    value: {
      type: String,
    },
  },
  methods: {},
};
</script>

<style>
@import "../css-variables.css";

.cell {
  background: #aaa;
  border-radius: 1vw;
  display: flex;
  justify-content: center;
  align-items: center;
  transition: background-color 1s;
}
</style>
