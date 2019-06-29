<template>
  <div class="level-chooser">
    <h1>Choose Level</h1>

    <div class="levels">
      <div v-for="(map, i) in maps" :key="map" class="level" @click="chooseLevel(i+1)">
        <level-thumbnail :map="map"></level-thumbnail>
      </div>
    </div>
  </div>
</template>

<script>
import LevelThumbnail from "./LevelThumbnail.vue";
import maps from "../maps.js";

export default {
  name: "LevelChooser",
  components: {
    LevelThumbnail
  },
  data() {
    return {
      maps,
    };
  },
  methods: {
    chooseLevel(levelNo) {
      this.$emit("level-chosen", levelNo);
    },
  },
};
</script>

<style scoped>
  .level-chooser {
    color: #444;
    background-color: white;

    padding: 20px 30px;

    position: fixed;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;

    z-index: 5;

    overflow: scroll;
  }
  .levels {
    display: flex;
    flex-wrap: wrap;
  }
  .level {
    border: 1px solid black;

    margin: 7px;

    width: 200px;
    height: 200px;

    display: flex;
    justify-content: center;
    align-items: center;

    cursor: pointer;
  }
  .level:hover {
    border-color: red;
    border-width: 3px;
    margin: 5px;
  }
</style>
