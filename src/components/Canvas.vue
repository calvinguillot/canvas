<template>
  <v-container fluid class="pa-0 ma-0" style="background:white; height: 100vh;">
    <canvas id="c1" :width="wWidth" :height="wHeight"></canvas>
    <v-row>
      <v-col cols="6" sm="3" md="2" class="panelPOS">
        <v-row class="mt-3">
          <v-col class="pb-0" elevation="15">
            <v-slider
              v-model="size"
              color="black"
              :thumb-size="24"
              thumb-label="always"
              track-color="grey lighten-3"
              min="0"
              max="30"
            ></v-slider>
          </v-col>
          <v-col class="pt-0">
            <v-color-picker v-model="colour" elevation="10" hide-inputs></v-color-picker>
          </v-col>
          <v-col class="pt-0">
            <v-row>
              <v-col v-for="button in buttons" :key="button.icon" cols="4" align="center">
                <v-btn
                  x-small
                  fab
                  text
                  color="black"
                  elevation="10"
                  @click="actions(button.function)"
                >
                  <v-icon>{{button.icon}}</v-icon>
                </v-btn>
              </v-col>
            </v-row>
          </v-col>
        </v-row>
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
import { fabric } from "fabric";
export default {
  name: "Canvas",
  data() {
    return {
      canvas: null,
      wHeight: null,
      wWidth: null,
      brushCursor: null,
      colour: null,
      size: null,
      buttons: [
        { icon: "mdi-close", function: "clear" },
        { icon: "mdi-replay", function: "undo" },
        { icon: "mdi-download", function: "save" },
      ],
    };
  },
  methods: {
    actions(e) {
      if (e === "clear") {
        this.clearCanvas();
      } else if (e === "undo") {
        this.undoDraw();
      } else if (e === "save") {
        this.saveImage();
      }
    },
    clearCanvas() {
      this.canvas.clear().renderAll();
    },
    undoDraw() {
      let objects = this.canvas.getObjects();
      if (objects.length > 1) {
        this.canvas.remove(objects[objects.length - 1]).renderAll();
      }
    },
    saveImage() {
      let today = new Date();
      let date =
        today.getFullYear() +
        "-" +
        (today.getMonth() + 1) +
        "-" +
        today.getDate();
      let time =
        today.getHours() + ":" + today.getMinutes() + ":" + today.getSeconds();
      let dateTime = date + "_" + time;

      this.canvas.setBackgroundColor("rgba(255, 255, 255, 1)").renderAll();
      let a = document.createElement("a");
      a.href = this.canvas.toDataURL({
        format: "png",
      });
      a.setAttribute("download", "Canvas_" + dateTime);
      a.click();
    },
  },
  updated() {
    // Brush Size
    this.canvas.freeDrawingBrush.width = this.size;
    this.brushCursor
      .set({ radius: this.size / 2 })
      .setCoords()
      .canvas.renderAll();
    // Brush Colour
    this.canvas.freeDrawingBrush.color = this.colour.hexa;
    this.brushCursor
      .set({
        fill: this.colour.hexa,
      })
      .setCoords()
      .canvas.renderAll();
  },
  mounted() {
    let self = this;
    this.canvas = new fabric.Canvas("c1");
    this.canvas.isDrawingMode = true;

    this.size = 10;

    this.brushCursor = new fabric.Circle({
      left: 0,
      top: 0,
      radius: 5 / 2,
      fill: this.colour.hexa,
      originX: "left",
      originY: "top",
    });
    this.canvas.add(this.brushCursor);

    this.canvas.on("mouse:move", function (obj) {
      self.brushCursor.top = obj.e.y - self.brushCursor.radius;
      self.brushCursor.left = obj.e.x - self.brushCursor.radius;
      self.canvas.renderAll();
    });
  },
  created() {
    this.wWidth = window.innerWidth;
    this.wHeight = window.innerHeight;
  },
};
</script>

<style>
::-webkit-scrollbar {
  display: none;
}

body {
  overflow: hidden;
}

.panelPOS {
  position: absolute;
  top: 0%;
  left: 0%;
  width: 100%;
  z-index: 10;
}
</style>