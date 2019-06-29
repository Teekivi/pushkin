<template>
  <div id="app">
    <world ref="world" @game-won="increaseLevel"></world>

    <level-chooser v-if="levelChooserVisible" @level-chosen="chooseLevel">
    </level-chooser>

    <div class="bottom-bar">
      <a href="#" @click="restartLevel">⟲</a><br/>
      <a href="#" @click="decreaseLevel">◄</a>
      <a href="#" @click="showLevelChooser">level {{ levelNo }}</a>
      <a href="#" @click="increaseLevel">►</a>
    </div>

    <a href="https://github.com/Teekivi/pushkin">
      <img style="position: absolute; top: 0; right: 0; border: 0; z-index: 10;"
           src="https://s3.amazonaws.com/github/ribbons/forkme_right_gray_6d6d6d.png" alt="Fork me on GitHub">
    </a>
  </div>
</template>

<script>
import maps from "./maps.js";
import LevelChooser from "./components/LevelChooser.vue";
import World from "./components/World.vue";

export default {
  name: 'app',
  components: {
    LevelChooser, World,
  },
  data() {
    return {
      levelChooserVisible: false,
      levelNo: -1,
    };
  },
  created() {
    this.levelNo = 1;
  },
  watch: {
    levelNo(val) {
      if (val >= 1 && val <= maps.length) {
        this.$refs.world.loadMapFromString(maps[val - 1]);
      }
    },
  },
  methods: {
    decreaseLevel() {
      if (this.levelNo > 1) {
        this.levelNo--;
      }
    },
    increaseLevel() {
      if (this.levelNo < maps.length) {
        this.levelNo++;
      }
    },
    chooseLevel(levelNo) {
      this.levelNo = levelNo;
      this.levelChooserVisible = false;
    },
    restartLevel() {
      this.$refs.world.loadMapFromString(maps[this.levelNo - 1]);
    },
    showLevelChooser() {
      this.levelChooserVisible = true;
    },
  },
};
</script>

<style>
  html, body {
    height: 100%;
    box-sizing: content-box;
    margin: 0;
  }
</style>

<style scoped>
  #app {
    height: 100%;
  }

  .bottom-bar {
    background-color: white;
    color: #222;
    font-size: 2rem;
    position: fixed;
    bottom: 0;
    left: 0;
    right: 0;
    text-align: center;
    padding-bottom: 5px;
  }

  a:link, a:visited {
    color: #555;
    text-decoration: none;
  }
  a:hover, a:active {
    color: black;
  }
</style>
