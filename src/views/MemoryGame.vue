<template>
  <div class="main-container">
    <score-bar
      :score="score"
      :hiScore="hiScore"
      :gamesWon="gamesWon"
      :scoreType="scoreType"
      @reset="resetGame"
    ></score-bar>
    <div class="app-header">
      <h1 class="main-title">MEMORY MATCH</h1>
      <h3 class="main-subtitle">Turn over the cards to create matches!</h3>
    </div>
    <div class="game-container">
      <card
        v-for="(val, index) in values"
        :key="index"
        :value="val"
        @selected="handleSelection"
      ></card>
    </div>
  </div>
</template>

<script>
import axios from "axios";
import card from "../components/card";
import scoreBar from "../components/scoreBar";

export default {
  components: { card, scoreBar },
  data() {
    return {
      values: [],
      selected: [],
      matchCount: 0,
      gamesWon: 0,
      // add multiplier when back to back matches
      score: 0,
      hiScore: 0,
      clicked: false,
      scoreType: "none",
      streak: 0,
    };
  },
  beforeMount() {
    this.initializeData();
  },
  methods: {
    initializeData() {
      axios
        .get(
          "https://raw.githubusercontent.com/terakeet/candidate-assignment-software-frontend/main/src/data/data.json"
        )
        .then((res) => {
          const values = res.data.data;
          let valArr = [];
          let id = 0;

          values.forEach((val) => {
            id++;
            const obj = {
              value: val,
              selected: false,
              matched: false,
              id: id,
            };
            valArr.push(obj);
          });

          values.forEach((val) => {
            id++;
            const obj = {
              value: val,
              selected: false,
              matched: false,
              id: id,
            };
            valArr.push(obj);
          });

          for (let i = valArr.length - 1; i > 0; i--) {
            let j = Math.floor(Math.random() * (i + 1));
            [valArr[i], valArr[j]] = [valArr[j], valArr[i]];
          }

          this.values = valArr;
        })
        .catch((err) => {
          console.log(err);
        });
    },
    handleSelection(val) {
      if (this.clicked) {
        return;
      }
      this.clicked = true;

      const alreadySelected = this.selected.some((item) => {
        if (item.id === val.id) {
          return true;
        }
      });
      if (!alreadySelected) {
        const idMatch = (item) => item.id === val.id;
        const index = this.values.findIndex(idMatch);
        this.values[index].selected = true;
        this.selected.push(val);
      }

      setTimeout(() => {
        this.clicked = false;
      }, 800);
    },
    checkSelections() {
      if (
        this.selected[0].value === this.selected[1].value &&
        this.selected[0].id != this.selected[1].id
      ) {
        const firstMatch = (item) => item.id === this.selected[0].id;
        const secondMatch = (item) => item.id === this.selected[1].id;
        const firstIndex = this.values.findIndex(firstMatch);
        const secondIndex = this.values.findIndex(secondMatch);
        this.values[firstIndex].matched = true;
        this.values[secondIndex].matched = true;
        this.scoreType = "positive";
        if (this.streak > 1) {
          this.score += 100 + 10 * this.streak;
        } else {
          this.score += 100;
        }
        setTimeout(() => {
          this.scoreType = "none";
        }, 800);
        this.matchCount++;
        this.streak++;
      } else {
        if (this.score >= 50) {
          this.scoreType = "negative";
          this.score -= 50;
          setTimeout(() => {
            this.scoreType = "none";
          }, 800);
        }
        this.streak = 0;
      }
      setTimeout(() => {
        const fourthMatch = (item) => item.id === this.selected[0].id;
        const fithMatch = (item) => item.id === this.selected[1].id;
        const firstIndex = this.values.findIndex(fourthMatch);
        const secondIndex = this.values.findIndex(fithMatch);
        this.values[firstIndex].selected = false;
        this.values[secondIndex].selected = false;
        this.selected = [];
      }, 800);
    },
    resetGame() {
      setTimeout(() => {
        if (this.matchCount > 5) {
          this.gamesWon += 1;
        }
        this.matchCount = 0;
        this.selected = [];
        this.score > this.hiScore ? (this.hiScore = this.score) : "";
        this.score = 0;

        this.initializeData();
      }, 900);
    },
  },
  watch: {
    selected() {
      if (this.selected.length === 2 && this.matchCount < 6) {
        this.checkSelections();
      }
    },
    matchCount() {
      if (this.matchCount > 5) {
        this.resetGame();
      }
    },
  },
};
</script>

<style lang="scss">
.main-container {
  height: 100vh;
  padding: 1rem 4rem;
  background-color: $primary;
  background-image: linear-gradient(170deg, $primary 10%, white 100%);
}
.app-header {
  width: 100%;
  text-align: center;
}
.main-title,
.main-subtitle {
  color: white;
}
.main-title {
  text-shadow: 2px 2px 2px #000000;
  margin-bottom: 0;
}
.main-subtitle {
  text-shadow: 2px 2px 1px #000000;
  margin-top: 1rem;
}
.game-container {
  margin: auto;
  width: 100%;
  max-width: 560px;
  display: grid;
  grid-template-columns: auto auto auto auto;
  justify-content: space-evenly;
}

@media only screen and (max-width: 560px) {
  .main-container {
    padding-top: 0.1rem;
  }
  .game-container {
    grid-template-columns: auto auto auto;
  }
}
</style>