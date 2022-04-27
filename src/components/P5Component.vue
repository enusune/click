<template>
  <div class="p5-container" ref="p5"></div>
</template>

<script>
import p5 from "p5";
function distinct(arr) {
  return Array.from(new Set(arr));
}
const initialEvents = [
  "preload",
  "setup",
  "draw",
  "deviceMoved",
  "deviceTurned",
  "deviceShaken",
  "keyPressed",
  "keyReleased",
  "keyTyped",
  "mouseMoved",
  "mouseDragged",
  "mousePressed",
  "mouseReleased",
  "mouseClicked",
  "doubleClicked",
  "mouseWheel",
  "touchStarted",
  "touchMoved",
  "touchEnded",
  "windowResized",
];
export default {
  computed: {
    p5Events() {
      const { additionalEvents } = this;
      return Array.isArray(additionalEvents)
        ? distinct(initialEvents.concat(additionalEvents))
        : initialEvents;
    },
  },
  mounted() {
    this.$nextTick(() => {
      new p5(sketch => {
        this.$emit("sketch", sketch);
        for (const p5EventName of this.p5Events) {
          const vueEventName = p5EventName.toLowerCase();
          const savedCallback = sketch[p5EventName];
          sketch[p5EventName] = (...args) => {
            if (savedCallback) {
              savedCallback(sketch, ...args);
            }
            this.$emit(vueEventName, sketch, ...args);
          };
        }
      }, this.$el);
    });
  },
};
</script>
<script setup>
defineProps({
  additionalEvents: Object,
});
</script>

<style></style>
