<template>
  <div class="container">
    <p5-component
      @preload="preload"
      @setup="setup"
      @draw="draw"
      @keypressed="keyPressed"
      @mousemoved="mouseMoved"
      @mousedragged="mouseDragged"
    ></p5-component>
  </div>
</template>

<script lang="ts">
import { defineComponent } from "vue";
import P5Component from "./P5Component.vue";
export default defineComponent({
  components: { P5Component },
  data() {
    return {
      rects: [] as any,
    };
  },
  methods: {
    preload(sketch: any) {},
    setup(sketch: any) {
      // debugger;
      const width = window.innerWidth;
      const height = window.innerHeight;
      const final = Math.min(width, height);
      sketch.createCanvas(final, final);
      sketch.background(0);
      this.rects.push({
        x: 0,
        y: 0,
        w: window.innerHeight,
        h: window.innerHeight,
      });
    },
    draw(sketch: any) {
      sketch.mouseClicked = () => {
        // debugger;
        if (sketch.mouseButton == sketch.LEFT) {
          const rects = this.rects;
          const mouseX = sketch.mouseX;
          const mouseY = sketch.mouseY;
          let found = -1;
          for (let index = 0; index < rects.length; index++) {
            if (
              mouseX > rects[index].x &&
              mouseX < rects[index].w + rects[index].x &&
              mouseY > rects[index].y &&
              mouseY < rects[index].h + rects[index].y
            ) {
              found = index;
            }
          }
          const r = rects[found];
          let a, b;
          if (r.w < r.h) {
            a = { x: r.x, y: r.y, w: r.w, h: r.h / 2 };
            b = {
              x: r.x,
              y: r.y + r.h / 2,
              w: r.w,
              h: r.h / 2,
            };
            rects.splice(found, 1);
            rects.push(a);
            rects.push(b);
            this.rects = rects;
          } else {
            a = { x: r.x, y: r.y, w: r.w / 2, h: r.h };
            b = {
              x: r.x + r.w / 2,
              y: r.y,
              w: r.w / 2,
              h: r.h,
            };
            rects.splice(found, 1);
            rects.push(a);
            rects.push(b);
            this.rects = rects;
          }

          sketch.fill(
            Math.random() * 255,
            Math.random() * 255,
            Math.random() * 255,
          );
          sketch.rect(a.x, a.y, a.w, a.h);
          sketch.fill(
            Math.random() * 255,
            Math.random() * 255,
            Math.random() * 255,
          );
          sketch.rect(b.x, b.y, b.w, b.h);
        }
      };

      function drawRect(x: any, y: any, w: any, h: any, sketch: any) {
        sketch.rect(x, y, w, h);
      }
    },

    keyPressed() {},
    mouseMoved() {},
    mouseDragged() {},
    toggleRed() {},
    toggleGreen() {},
    pushLine() {},
  },
});
</script>

<style lang="scss" scoped>
.container {
  display: flex;
  align-items: center;
  justify-content: center;
  min-height: 90vh;
  padding: 0 10px;
  box-sizing: border-box;
}
</style>
