<template>
  <div class="container">
    <div class="toolbar">
      <div class="tool_item">
        <div class="label">分裂方向：</div>
        <el-radio-group v-model="radio">
          <el-radio :label="0">横向</el-radio>
          <el-radio :label="1">纵向</el-radio>
          <el-radio :label="2">随机</el-radio>
        </el-radio-group>
      </div>
      <div class="tool_item">
        <div class="label">颜色：</div>
        <el-radio-group v-model="colorSelect">
          <el-radio :label="0">随机</el-radio>
          <el-radio :label="1">选择</el-radio>
        </el-radio-group>
      </div>
      <div v-if="colorSelect" class="tool_item">
        <div class="label">颜色1：</div>
        <el-color-picker v-model="color1" size="large" />
      </div>
      <div v-if="colorSelect" class="tool_item">
        <div class="label">颜色2：</div>
        <el-color-picker v-model="color2" size="large" />
      </div>
    </div>
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
      radio: 2,
      colorSelect: 0,
      color1: "#ffffff",
      color2: "#000000",
    };
  },
  methods: {
    preload(sketch: any) {},
    setup(sketch: any) {
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
          } else if (r.w > r.h) {
            a = { x: r.x, y: r.y, w: r.w / 2, h: r.h };
            b = {
              x: r.x + r.w / 2,
              y: r.y,
              w: r.w / 2,
              h: r.h,
            };
          } else {
            let is = Math.random() * 10 > 5 ? 0 : 1;
            if (this.radio < 2) {
              is = this.radio;
            }
            if (is) {
              a = { x: r.x, y: r.y, w: r.w / 2, h: r.h };
              b = {
                x: r.x + r.w / 2,
                y: r.y,
                w: r.w / 2,
                h: r.h,
              };
            } else {
              a = { x: r.x, y: r.y, w: r.w, h: r.h / 2 };
              b = {
                x: r.x,
                y: r.y + r.h / 2,
                w: r.w,
                h: r.h / 2,
              };
            }
          }
          rects.splice(found, 1);
          rects.push(a);
          rects.push(b);
          this.rects = rects;
          const c1 = this.set16ToRgb(this.color1);
          const c2 = this.set16ToRgb(this.color2);
          if (!this.colorSelect) {
            sketch.fill(
              Math.random() * 255,
              Math.random() * 255,
              Math.random() * 255,
            );
          } else {
            sketch.fill(c1[0], c1[1], c1[2]);
          }
          sketch.rect(a.x, a.y, a.w, a.h);
          if (!this.colorSelect) {
            sketch.fill(
              Math.random() * 255,
              Math.random() * 255,
              Math.random() * 255,
            );
          } else {
            sketch.fill(c2[0], c2[1], c2[2]);
          }
          sketch.rect(b.x, b.y, b.w, b.h);
        }
      };
    },

    keyPressed() {},
    mouseMoved() {},
    mouseDragged() {},
    toggleRed() {},
    toggleGreen() {},
    pushLine() {},
    set16ToRgb(str: string): number[] {
      var reg = /^#([0-9A-Fa-f]{3}|[0-9A-Fa-f]{6})$/;
      if (!reg.test(str)) {
        return [];
      }
      let newStr = str.toLowerCase().replace(/\#/g, "");
      let len = newStr.length;
      if (len == 3) {
        let t = "";
        for (var i = 0; i < len; i++) {
          t += newStr.slice(i, i + 1).concat(newStr.slice(i, i + 1));
        }
        newStr = t;
      }
      let arr = []; //将字符串分隔，两个两个的分隔
      for (var i = 0; i < 6; i = i + 2) {
        let s = newStr.slice(i, i + 2);
        arr.push(parseInt("0x" + s));
      }
      return arr;
    },
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
  // flex-direction: column;
}

.toolbar {
  padding: 50px;
  // position: absolute;
  left: 0;
}
.tool_item {
  padding: 10px 0;
  display: flex;
  align-items: center;
  justify-content: flex-start;
  .label {
    margin-right: 10px;
    font-family: monospace;
  }
}
</style>
