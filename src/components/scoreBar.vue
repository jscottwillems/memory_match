<template>
  <div class="score-bar">
    <div class="score">
      <h4>
        SCORE: <span :class="computedClass">{{ score }}</span>
      </h4>
    </div>
    <div class="games-played">
      <h4>GAMES WON: {{ gamesWon }}</h4>
    </div>
    <div>
      <div class="restart" @click="$emit('reset')"><Reload fillColor="#ffffff" /></div>
    </div>
    <div class="hi-score">
      <h4>HI-SCORE: {{ hiScore }}</h4>
    </div>
  </div>
</template>

<script>
import Reload from "vue-material-design-icons/Reload.vue";

export default {
  props: {
    score: {
      type: Number,
      default: 0,
    },
    hiScore: {
      type: Number,
      default: 0,
    },
    gamesWon: {
      type: Number,
      default: 0,
    },
    scoreType: {
      type: String,
      default: "none",
    },
  },
  components: { Reload },
  computed: {
    computedClass() {
      switch (this.scoreType) {
        case "positive":
          return "score-up";
        case "negative":
          return "score-down";
        default:
          return "";
      }
    },
  },
};
</script>

<style lang="scss">
.score-bar {
  display: flex;
  justify-content: space-between;
  align-items: center;

  h4 {
    margin: 0;
    padding: 0;
    color: white;
    text-shadow: 1px 1px 0px #000000;
  }
}

.score,
.games-played,
.hi-score,
.restart {
  width: 150px;
  transition: all 0.3s linear;
}

.restart {
  filter: drop-shadow(1px 1px 0px #000000);
  cursor: pointer;
  width: auto;
  height: auto;

  &:hover {
    transform: rotate(0.4turn);
    transform-origin: center;
  }
}

.score-up {
  color: #7cfc00;
}

.score-down {
  color: #ff0000;
}
</style>