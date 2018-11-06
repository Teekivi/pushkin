<template>
  <div class="world" :style="{ width: worldSize[0] + 'px', height: worldSize[1] + 'px' }">
    <div v-for="pos of walls" class="part wall" :class="`x-${pos[0]} y-${pos[1]}`"></div>
    <div v-for="pos of hearts" class="part heart" :class="`x-${pos[0]} y-${pos[1]}`"></div>
    <div v-for="pos of slots" class="part slot" :class="`x-${pos[0]} y-${pos[1]}`"></div>
    <div class="part player" v-if="player[0] !== -1" :class="`x-${player[0]} y-${player[1]}`"></div>
  </div>
</template>

<script>
export default {
  name: "World",
  data() {
    return {
      walls: [],
      hearts: [],
      slots: [],
      player: [-1, -1],

      worldSize: [],
    };
  },
  created() {
    window.addEventListener("keydown", this.onKeyDown);
  },
  destroyed() {
    window.removeEventListener("keydown", this.onKeyDown);
  },
  methods: {
    loadMapFromString(mapString) {
      // clear the state
      this.walls.length = 0;
      this.hearts.length = 0;
      this.slots.length = 0;
      this.player.length = 0;

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
      this.worldSize[0] = maxWidth * 25;
      this.worldSize[1] = y * 25;
    },

    indexOfSubList(haystack, needle) {
      for (const i in haystack) {
        if (haystack[i].length === needle.length) {
          let failed = false;
          for (const j in haystack) {
            if (haystack[i][j] !== needle[j]) {
              failed = true;
              break;
            }
          }
          if (!failed) {
            return i;
          }
        }
      }
      return -1;
    },

    onKeyDown(event) {
      const code = event.code;
      if (code.startsWith("Arrow")) {
        // Attempt to move the player

        let movement;
        if (code === "ArrowLeft") {
          movement = [-1, 0];
        } else if (code === "ArrowRight") {
          movement = [1, 0];
        } else if (code === "ArrowUp") {
          movement = [0, -1];
        } else {  // code === "ArrowDown"
          movement = [0, 1];
        }

        const playerDestPosition = [
          this.player[0] + movement[0],
          this.player[1] + movement[1],
        ];

        const heartIndex = this.indexOfSubList(this.hearts, playerDestPosition);
        if (heartIndex !== -1) {
          const heartDestPosition = [
            playerDestPosition[0] + movement[0],
            playerDestPosition[1] + movement[1],
          ];

          if (this.indexOfSubList(this.walls, heartDestPosition) === -1 &&
                this.indexOfSubList(this.hearts, heartDestPosition) === -1) {
            this.hearts[heartIndex] = heartDestPosition;
            this.player = playerDestPosition;

            if (this.indexOfSubList(this.slots, heartDestPosition) !== -1) {
              // The heart was pushed into a slot.
              // Check if the level is completed.
              let gameWon = true;
              for (const heart of this.hearts) {
                if (this.indexOfSubList(this.slots, heart) === -1) {
                  gameWon = false;
                }
              }
              if (gameWon) {
                this.$root.$emit("game-won");
              }
            }
          }
        } else if (this.indexOfSubList(this.walls, playerDestPosition) === -1) {
          this.player = playerDestPosition;
        }
      }
    },
  },
};
</script>

<style lang="scss" scoped>
  $part-width: 25px;
  $part-height: 25px;

  .world {
    position: relative;
  }

  .world .part {
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
