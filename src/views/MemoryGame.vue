<template>
  <div class="main-container">
    <score-bar
      :score="score"
      :hiScore="hiScore"
      :gamesWon="gamesWon"
      :scoreType="scoreType"
      @reset="resetGame(0)"
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
import Vue from "vue";
import VueConfetti from "vue-confetti";
import axios from "axios";
import card from "../components/card";
import scoreBar from "../components/scoreBar";

Vue.use(VueConfetti);

export default {
  components: { card, scoreBar },
  data() {
    return {
      values: [],
      selected: [],
      matchCount: 0,
      gamesWon: 0,
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

          // take each value and add additional properties then push to array
          // repeat the process so we end up with two of each value each with unique IDs
          for (let i = 0; i < 2; i++) {
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
          }

          // randomize the order of the full array before setting it in the data
          for (let i = valArr.length - 1; i > 0; i--) {
            let j = Math.floor(Math.random() * (i + 1));
            [valArr[i], valArr[j]] = [valArr[j], valArr[i]];
          }

          this.values = valArr;
        })
        .catch((err) => {
          // eslint-disable-next-line
          console.log(err);
        });
    },
    handleSelection(val) {
      // check to prevent double clicking
      if (this.clicked) {
        return;
      }
      this.clicked = true;

      // checks to see if card being clicked has already been selected
      const alreadySelected = this.selected.some((item) => {
        if (item.id === val.id) {
          return true;
        }
      });

      // if card hasn't been selected, set the "selected" prop on the data obj to true and push to selected array
      if (!alreadySelected) {
        const idMatch = (item) => item.id === val.id;
        const index = this.values.findIndex(idMatch);
        this.values[index].selected = true;
        this.selected.push(val);
      }

      // allows time for css transition before setting "clicked" back to false
      setTimeout(() => {
        this.clicked = false;
      }, 800);
    },
    checkSelections() {
      const firstMatch = (item) => item.id === this.selected[0].id;
      const secondMatch = (item) => item.id === this.selected[1].id;
      const firstIndex = this.values.findIndex(firstMatch);
      const secondIndex = this.values.findIndex(secondMatch);

      // check if both selections match and are not the same card
      if (
        this.selected[0].value === this.selected[1].value &&
        this.selected[0].id != this.selected[1].id
      ) {
        // if both selections match set "matched" prop on data obj to true and increase score
        this.values[firstIndex].matched = true;
        this.values[secondIndex].matched = true;

        // adds the color class to the score keeper and increases the player's score
        this.scoreType = "positive";

        // if player gets 2 or more matches in a row score gets extra multiplier
        if (this.streak > 1) {
          this.score += 100 + 10 * this.streak;
        } else {
          this.score += 100;
        }

        // removes color class from score keeper
        setTimeout(() => {
          this.scoreType = "none";
        }, 800);

        // keeps track of number of matches and number of matches in a row
        this.matchCount++;
        this.streak++;
      } else {
        // if selections dont match deduct points from score
        if (this.score >= 50) {
          this.scoreType = "negative";
          this.score -= 50;
          setTimeout(() => {
            this.scoreType = "none";
          }, 800);
        }
        this.streak = 0;
      }

      // after css transistions finish set "selected" prop on data objs back to false
      setTimeout(() => {
        this.values[firstIndex].selected = false;
        this.values[secondIndex].selected = false;
        this.selected = [];
      }, 800);
    },
    resetGame(timeOut) {
      setTimeout(() => {
        // if game is completed increase val for "gamesWon"
        if (this.matchCount > 5) {
          this.gamesWon += 1;
        }

        // reset game vals and check current score against hi-score, if new hi-score replace old
        this.matchCount = 0;
        this.selected = [];
        this.score > this.hiScore ? (this.hiScore = this.score) : "";
        this.score = 0;

        this.initializeData();
      }, timeOut);
    },
  },
  watch: {
    selected() {
      // once two cards have been selected check them for match
      if (this.selected.length === 2 && this.matchCount < 6) {
        this.checkSelections();
      }
    },
    matchCount() {
      // checks to see if game has been completed
      if (this.matchCount > 5) {
        this.$confetti.start({ defaultDropRate: 30 });
        this.resetGame(900);
        setTimeout(() => {
          this.$confetti.stop();
        }, 3000);
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
  display: -ms-grid;
  grid-template-columns: auto auto auto auto;
  justify-content: space-evenly;
  -ms-grid-columns: auto auto auto auto;
  -webkit-box-pack: space-evenly;
  -ms-flex-pack: space-evenly;
}

@media only screen and (max-width: 560px) {
  .main-container {
    padding: 0.1rem 2rem;
  }
  .game-container {
    grid-template-columns: auto auto auto;
    -ms-grid-columns: auto auto auto;
  }
}
</style>