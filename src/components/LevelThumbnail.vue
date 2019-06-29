<template>
  <canvas ref="canvas" :width="canvasWidth" :height="canvasHeight"></canvas>
</template>

<script>
const BLOCK_SIZE = 8;

export default {
  name: "LevelThumbnail",
  props: {
    map: {
      type: String,
      required: true,
    },
  },
  data() {
    const mapLines = this.map.split("\n");
    const maxCols = Math.max(...mapLines.map((line) => line.length));
    return {
      canvasWidth: maxCols * BLOCK_SIZE,
      canvasHeight: mapLines.length * BLOCK_SIZE,
    };
  },
  mounted() {
    const ctx = this.$refs.canvas.getContext("2d");

    const lines = this.map.split("\n");
      for (const j in lines) {
        for (const i in lines[j]) {
          const letter = lines[j][i];
          if (letter === "X") {
            ctx.fillStyle = "brown";
          } else if (letter === "*") {
            ctx.fillStyle = "red";
          } else if (letter === ".") {
            ctx.fillStyle = "green";
          } else if (letter === "@") {
            ctx.fillStyle = "yellow";
          } else {
            continue;
          }
          ctx.fillRect(i*BLOCK_SIZE, j*BLOCK_SIZE, BLOCK_SIZE, BLOCK_SIZE);
        }
      }
  },
};
</script>
