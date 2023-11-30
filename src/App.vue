<script>
export default {
  data() {
    return {
      stateIdx: 0,
      states:   []
    }
  },
  methods: {
    digPatternName(pattern) {
        return pattern.Number || pattern.Action || pattern.Wild
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
        <li>現在の席: {{ states[stateIdx].current_seat_num }}</li>
        <li>周順: {{ states[stateIdx].is_normal_order ? "正順" : "逆順" }}</li>
        <li>現在の着手型: {{ states[stateIdx].action_type }}</li>
      </ul>
      <h3>場のカード</h3>
      <div :class="['card', states[stateIdx].table_card.color]">
        {{ digPatternName(states[stateIdx].table_card.pattern) }}
      </div>
    </div>
    <h2>プレイヤ情報</h2>
    <div>
      <table>
        <thead>
          <tr>
            <th class="seat-cell">席番号</th>
            <th class="player-hands-cell">手札</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="seatIdx in [0, 1, 2, 3]">
            <td :class="['seat-cell', {'current-player-cell' : seatIdx === states[stateIdx].current_seat_num}]">
              {{ seatIdx }}
            </td>
            <td :class="['player-hand-cell', {'current-player-cell' : seatIdx === states[stateIdx].current_seat_num}]">
              <div class="cards" v-if="states[stateIdx].player_hands[seatIdx] !== 'Empty'">
                <div v-for="card in states[stateIdx].player_hands[seatIdx]" :class="['card', card.color]">
                  {{ digPatternName(card.pattern) }}
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
  .Red {
    color: white;
    background-color: red;
  }
  .Yellow {
    color: black;
    background-color: yellow;
  }
  .Green {
    color: white;
    background-color: green;
  }
  .Blue {
    color: white;
    background-color: blue;
  }
  .Wild {
    color: white;
    background: linear-gradient(to bottom,
        black 40%, red 40% 55%, yellow 55% 70%, green 70% 85%, blue 85%);
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

  .player-hands-cell {
    width: 84%;
  }

  .current-player-cell {
    background-color: palevioletred;
  }
</style>
