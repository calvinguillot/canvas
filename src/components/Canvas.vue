<template>
  <v-container fluid class="pa-0" style="background:white; height: 100vh;">
    <v-row justify-center align-center style="height: 100vh; overflow: hidden;">
      <v-row align-center>
        <canvas id="c1" :width="wWidth" :height="wHeight"></canvas>
        <v-row class="panelPOS">
          <v-row>
            <v-col cols="12">
              <v-btn fab @click="brushSize(5)" class="my-3 mx-4" style=" height: 10px;width: 10px;"></v-btn>
              <v-btn
                fab
                @click="brushSize(10)"
                class="mb-3 mx-4"
                style=" height: 17px;width: 17px;"
              ></v-btn>
              <v-btn
                fab
                @click="brushSize(20)"
                class="mb-3 mx-4"
                style=" height: 25px;width: 25px;"
              ></v-btn>
            </v-col>
            <v-col cols="12">
              <v-btn
                v-for="(colour, index) in colours"
                :key="colour"
                fab
                class="ma-1"
                style=" height: 25px;width: 25px;"
                :color="coloursBTN[index]"
                @click="brushColour(colours[index])"
              ></v-btn>
            </v-col>
            <v-col cols="12" class="mt-3">
              <v-btn fab text class="my-0" color="white" @click="clearCanvas">
                <v-icon>mdi-close</v-icon>
              </v-btn>
              <v-btn fab text class="my-0" color="white" @click="undoDraw">
                <v-icon>mdi-replay</v-icon>
              </v-btn>
              <v-btn fab text class="my-0" color="white" @click="saveImage">
                <v-icon>mdi-download</v-icon>
              </v-btn>
            </v-col>
          </v-row>
        </v-row>
      </v-row>
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
      colours: [
        "#2ECC71",
        "#3498db",
        "#9b59b6",
        "#f1c40f",
        "#e67e22",
        "#e74c3c",
        "#000000",
        "#ffffff",
      ],
      coloursBTN: [
        "green",
        "blue",
        "purple",
        "yellow",
        "orange",
        "red",
        "black",
        "white",
      ],
    };
  },
  methods: {
    clearCanvas() {
      this.canvas.clear().renderAll();
    },
    brushSize(e) {
      this.canvas.freeDrawingBrush.width = e;
      this.brushCursor
        .set({ radius: e / 2 })
        .setCoords()
        .canvas.renderAll();
    },
    brushColour(e) {
      // console.log(e)
      this.canvas.freeDrawingBrush.color = e;
      this.brushCursor
        .set({
          fill: e,
        })
        .setCoords()
        .canvas.renderAll();
    },
    undoDraw() {
      let objects = this.canvas.getObjects();
      if (objects.length > 1) {
        this.canvas.remove(objects[objects.length - 1]).renderAll();
      }
    },
    saveImage() {
      this.canvas.setBackgroundColor("rgba(255, 255, 255, 1)").renderAll();
      let a = document.createElement("a");
      a.href = this.canvas.toDataURL({
        format: "png",
      });
      a.setAttribute("download", "I made this");
      a.click();
    },
  },
  mounted() {
    this.canvas = new fabric.Canvas("c1");
    this.canvas.isDrawingMode = true;
    this.brushCursor = new fabric.Circle({
      left: 0,
      top: 0,
      radius: 5 / 2,
      fill: this.colours[6],
      originX: "left",
      originY: "top",
    });
    this.canvas.add(this.brushCursor);
    let self = this;
    this.canvas.on("mouse:move", function (obj) {
      self.brushCursor.top = obj.e.y - self.brushCursor.radius;
      self.brushCursor.left = obj.e.x - self.brushCursor.radius;
      self.canvas.renderAll();
    });
    this.brushSize(5);
    this.brushColour(this.colours[6]);
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

.panelPOS {
  position: absolute;
  top: 100px;
  left: 0px;
  height: 450px;
  width: 75px;
  background-color: #424242;
  border-radius: 0px 20px 20px 0px;
}
</style>