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
  Skip:             "Skip",
  Reverse:          "Rev",
  DrawTwo:          "D2",
  Wild:             "W",
  WildDraw4:        "WD4",
  WildShuffleHands: "WSH",
  WildCustomizable: "WC"
};

export default {
  data() {
    return {
      stateIdx: 0,
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
      } else if (cardText.includes("WildDraw4")) {
        return UnoPattern.WildDraw4;
      } else if (cardText.includes("WildShuffleHands")) {
        return UnoPattern.WildShuffleHands;
      } else if (cardText.includes("WildCustomizable")) {
        return UnoPattern.WildCustomizable;
      } else if (cardText.includes("Wild")) {
        return UnoPattern.Wild;
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
    },
    incrementStateIdx() {
      this.stateIdx++;
      if (this.stateIdx >= this.states.length) {
        this.stateIdx = this.states.length - 1;
      }
    },
    decrimentStateIdx() {
      this.stateIdx--;
      if (this.stateIdx <= 0) {
        this.stateIdx = 0;
      }
    }
  }

}
</script>

<template>
  <div class="log-loading-area">
    <h2>ファイル読み込み</h2>
    <p>UnoStateのJSONを読み込ませてください。</p>
    <input type="file" ref="file" @change="loadLog"/>
  </div>
  <br>
  <div class="state-cursor-area" v-if="states.length > 0">
    <h2>表示中の状態</h2>
    <p>状態: {{ stateIdx + 1 }} / {{ states.length }}</p>
    <input type="button" value="前状態" @click="decrimentStateIdx">
    <input type="button" value="次状態" @click="incrementStateIdx">
  </div>
  <br>
  <div class="table-information-area" v-if="states.length > 0">
    <h2>テーブル情報</h2>
    <div>
      <ul>
        <li>現在のプレイヤ: {{ states[stateIdx].currentPlayer }}</li>
        <li>周順: {{ states[stateIdx].isNormalOrder ? "正順" : "逆順" }}</li>
        <li>現在の着手型: {{ states[stateIdx].currentActionType }}</li>
      </ul>
      <h3>場のカード</h3>
      <div :class="['card', parseColor(states[stateIdx].tableColor)]"> <!-- 場のカードは場の色の反映させたいので、白いワイルドのクラスは指定しない。 -->
        {{ parsePattern(states[stateIdx].tablePattern) }}
      </div>
    </div>
    <h2>プレイヤ情報</h2>
    <div>
      <table>
        <thead>
          <tr>
            <th class="seat-cell">席番号</th>
            <th class="score-cell">得点</th>
            <th class="player-cards-cell">手札</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="playerIdx in [0, 1, 2, 3]">
            <td :class="['seat-cell', {'current-player-cell' : playerIdx === states[stateIdx].currentPlayer}]">
              {{ states[stateIdx].playerSeats[playerIdx] }}
            </td>
            <td :class="['score-cell', {'current-player-cell' : playerIdx === states[stateIdx].currentPlayer}]">
              {{ states[stateIdx].playerScores[playerIdx] }}
            </td>
            <td :class="['player-cards-cell', {'current-player-cell' : playerIdx === states[stateIdx].currentPlayer}]">
              <div class="cards" v-if="states[stateIdx].playerCards[playerIdx] !== 'Empty'">
                <div v-for="card in states[stateIdx].playerCards[playerIdx]" :class="['card', parseColor(card), {'wild-customizable' : (parsePattern(card) === 'WC')}]">
                  {{ parsePattern(card) }}
                </div>
              </div>
            </td>
          </tr>
        </tbody>
      </table>
    </div>
  </div>
</template>

<style>
  .log-loading-area {
    background-color: palevioletred;
    margin: 1em 0;
    padding: 1em;
    width: 60em;
  }

  .state-cursor-area {
    background-color: paleturquoise;
    display: block;
    margin: 1em 0;
    padding: 1em;
    position: sticky;
    top: 0;
    width: 60em;
    z-index: 1;
  }

  .table-information-area {
    background-color: palegreen;
    display: block;
    margin: 1em 0;
    padding: 1em;
    width: 60em;
  }


  .cards {
    display: flex;
  }

  .card {
    width: 2.8em;
    height: 4em;
    border-radius: 0.25em;
    border: 0.1em solid black;
    flex-shrink: 0;
    padding: 0.1em;
    margin: 0.1em 0.1em;
    font-size: 1em;
    font-weight: bold;
    text-align: center;
    vertical-align: middle;
  }

  /* カードの色。 */
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
    background: linear-gradient(to bottom,
        black 40%, red 40% 55%, yellow 55% 70%, green 70% 85%, blue 85%);
  }

  /* カードの模様。色に優先。 */
  .wild-customizable {
    color: black;
    background: none; /* .wildの属性を無効化する。 */
    background-color: white;
  }

  table, th, td {
    border: 0.1em solid black;
    overflow-x: auto;
    table-layout: fixed;
    width: 100%;
  }
  th {
    background-color: greenyellow;
  }
  td {
    background-color: white;
  }

  .seat-cell {
    width: 8%;
  }

  .score-cell {
    width: 8%;
  }

  .player-cards-cell {
    width: 84%;
  }

  .current-player-cell {
    background-color: palevioletred;
  }
</style>
