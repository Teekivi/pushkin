<template>
  <div class="map" :style="{ width: mapSize[0] + 'px', height: mapSize[1] + 'px' }">
    <div v-for="pos of walls" class="part wall" :class="`x-${pos[0]} y-${pos[1]}`"></div>
    <div v-for="pos of hearts" class="part heart" :class="`x-${pos[0]} y-${pos[1]}`"></div>
    <div v-for="pos of slots" class="part slot" :class="`x-${pos[0]} y-${pos[1]}`"></div>
    <div class="part player" :class="`x-${player[0]} y-${player[1]}`"></div>
  </div>
</template>

<script>
export default {
  name: "Map",
  data() {
    return {
      walls: [],
      hearts: [],
      slots: [],
      player: [],

      mapSize: [],
    };
  },
  created() {
    this.loadMapFromString(
`    XXXXX
    X   X
    X*  X
  XXX  *XXX
  X  *  * X
XXX X XXX X     XXXXXX
X   X XXX XXXXXXX  ..X
X *  *             ..X
XXXXX XXXX X@XXXX  ..X
    X      XXX  XXXXXX
    XXXXXXXX`);
  },
  methods: {
    loadMapFromString(mapString) {
      let y = 0;
      let maxWidth = 0;
      for (const line of mapString.split("\n")) {
        let x = 0;
        for (const char of line) {
          if (char === "X") {
            this.walls.push([x, y]);
          } else if (char === "*") {
            this.hearts.push([x, y]);
          } else if (char === ".") {
            this.slots.push([x, y]);
          } else if (char === "@") {
            this.player[0] = x;
            this.player[1] = y;
          }
          x++;
        }
        if (x > maxWidth) {
          maxWidth = x;
        }
        y++;
      }
      this.mapSize[0] = maxWidth * 25;
      this.mapSize[1] = y * 25;
    },
  },
};
</script>

<style lang="scss" scoped>
  $part-width: 25px;
  $part-height: 25px;

  .map {
    position: relative;
  }

  .map .part {
    box-sizing: border-box;
    width: $part-width;
    height: $part-height;
    position: absolute;
  }

  .wall {
    border: 1px solid black;
    background-color: brown;
  }

  .part.heart {
    background-color: red;
    border-radius: 100%;
    margin: 3px;
    width: $part-width - 6;
    height: $part-height - 6;

    z-index: 10;
  }

  .part.slot {
    border: 1px dashed white;
    background-color: green;
  }

  .part.player {
    background-color: yellow;
    border: 1px solid orange;
    border-radius: 3px;

    z-index: 10;
  }

  @for $i from 0 through 30 {
    .part.x-#{$i} {
      left: $i * $part-width;
    }
    .part.y-#{$i} {
      top: $i * $part-height;
    }
  }
</style>
