<template>
  <div class="p5-container" ref="p5"></div>
</template>
<script lang="ts">
import { PropType, defineComponent } from "vue";

import p5 from "p5";
function distinct(arr: any) {
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
] as string[];
export default defineComponent({
  props: {
    additionalEvents: Object as PropType<string[]> | Object,
  },
  data() {
    return { initialEvents: [] };
  },
  computed: {
    p5Events(): string[] {
      return initialEvents;
    },
  },
  methods: {
    distinct(arr: string[]): string[] {
      return Array.from(new Set(arr));
    },
  },
  mounted() {
    this.$nextTick(() => {
      new p5((sketch) => {
        this.$emit("sketch", sketch);
        for (const p5EventName of this.p5Events) {
          const vueEventName = p5EventName.toLowerCase();
          const savedCallback = sketch[p5EventName];
          sketch[p5EventName] = (...args: string[]) => {
            if (savedCallback) {
              savedCallback(sketch, ...args);
            }
            this.$emit(vueEventName, sketch, ...args);
          };
        }
      }, this.$el);
    });
  },
});
</script>
