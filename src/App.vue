<script>

const UnoColor = {
  Red:    "red",
  Yellow: "yellow",
  Green:  "green",
  Blue:   "blue",
  Wild:   "wild"
};

const UnoPattern = {
  Zero:             "0",
  One:              "1",
  Two:              "2",
  Three:            "3",
  Four:             "4",
  Five:             "5",
  Six:              "6",
  Seven:            "7",
  Eight:            "8",
  Nine:             "9",
  Skip:             "skip",
  Reverse:          "reverse",
  DrawTwo:          "draw_two",
  Wild:             "wild",
  WildDraw4:        "wild_draw_4",
  WildShuffleHands: "wild_shuffle_hands",
  WildCustomizable: "wild_customizable"
};

export default {
  data() {
    return {
      state_idx: 0,
      states:    []
    }
  },
  methods: {
    parseColor(cardText) {
      switch (cardText[0]) {
        case "R":
          return UnoColor.Red;
        case "Y":
          return UnoColor.Yellow;
        case "G":
          return UnoColor.Green;
        case "B":
          return UnoColor.Blue;
        default:
          return UnoColor.Wild;
      }
    },
    parsePattern(cardText) {
      if (cardText.includes("DrawTwo")) {
        return UnoPattern.DrawTwo;
      } else if (cardText.includes("Reverse")) {
        return UnoPattern.Reverse;
      } else if (cardText.includes("Skip")) {
        return UnoPattern.Skip;
      } else if (cardText.includes("Wild")) {
        return UnoPattern.Wild;
      } else if (cardText.includes("WildDraw4")) {
        return UnoPattern.WildDraw4;
      } else if (cardText.includes("WildShuffleHands")) {
        return UnoPattern.WildShuffleHands;
      } else if (cardText.includes("WildCustomizable")) {
        return UnoPattern.WildCustomizable;
      } else if (cardText.includes("0")) {
        return UnoPattern.Zero;
      } else if (cardText.includes("1")) {
        return UnoPattern.One;
      } else if (cardText.includes("2")) {
        return UnoPattern.Two
      } else if (cardText.includes("3")) {
        return UnoPattern.Three;
      } else if (cardText.includes("4")) {
        return UnoPattern.Four;
      } else if (cardText.includes("5")) {
        return UnoPattern.Five;
      } else if (cardText.includes("6")) {
        return UnoPattern.Six;
      } else if (cardText.includes("7")) {
        return UnoPattern.Seven;
      } else if (cardText.includes("8")) {
        return UnoPattern.Eight;
      } else if (cardText.includes("9")) {
        return UnoPattern.Nine;
      }
    },
    loadLog() {
      const file = this.$refs.file.files[0];
      const reader = new FileReader()
      reader.onload = (event) => {
        this.states = JSON.parse(event.target.result);
      }
      reader.readAsText(file)
    }
  }

}
</script>

<template>
  <input type="file" ref="file" @change="loadLog"/>
  <br>
  <table v-if="states.length > 0">
    <thead>
      <tr><th>カード</th></tr>
    </thead>
    <tbody>
      <tr v-for="playerCards in states[state_idx].playerCards">
        <td>
          <div v-for="card in playerCards" :class="parseColor(card)">
          {{ card }}
          </div>
        </td>
      </tr>
    </tbody>
  </table>
</template>

<style>
  .red {
    color: white;
    background-color: red;
  }
  .yellow {
    color: black;
    background-color: yellow;
  }
  .green {
    color: white;
    background-color: green; 
  }
  .blue {
    color: white;
    background-color: blue; 
  }
  .wild {
    color: white;
    background-color: black; 
  }
</style>
