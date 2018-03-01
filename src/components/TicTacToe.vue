<template>
  <div>
    <div>
      <div class='player' :class='(currentPlayer==0) ? "current_player": ""'> Player 1 (X)</div>
      <div class='player' :class='(currentPlayer==1) ? "current_player": ""'> Player 2 (O)</div>
    </div>
    <div class='turn'>
        <span> Turn: </span> <span class='current_player'> {{players[currentPlayer]}}</span>
    </div>
    <div>
      <table>
        <tr v-for="(row, rowIdx) in gameState">
          <td v-for="(cell, columnIdx) in row" class='cell' :class="cell.status" @click="onClickCell(rowIdx, columnIdx)">{{cell.text}}</td>
        </tr>
      </table>
    </div>
    <div class='game-result'>
      {{result}}
    </div>
    <div v-show="result != ''">
      <button @click='resetGame'> RESET </button>
    </div>
  </div>

</template>

<script>
export default {
  name: 'TicTacToe',
  data () {
    return {
      players: [
        "Player 1 (X)",
        "Player 2 (0)",
      ],
      currentPlayer: 0,
      turnCount: 0,
      gameState: [
        [{status: '', text: ''}, {status: '', text: ''}, {status: '', text: ''}],
        [{status: '', text: ''}, {status: '', text: ''}, {status: '', text: ''}],
        [{status: '', text: ''}, {status: '', text: ''}, {status: '', text: ''}],
      ],
      result: '',
      endGameMessage: [
        'Player 1: win',
        'Player 2: win',
        'Draw',
        ''
      ]
    }
  },
  methods: {
    resetGame() {
      this.result = '';
      this.currentPlayer = 0;
      this.turnCount = 0;
      let len = this.gameState.length;
      for (let i = 0; i < len; i++) {
        this.gameState.splice(i, 1, [{status: '', text: ''}, {status: '', text: ''}, {status: '', text: ''}]);
      }
    },
    onClickCell (rowIdx, columnIdx) {
      // console.log('onClickCell')
      // console.log(this.result)
      if (this.result != '') return;
      // console.log(this.result)
      if (this.gameState[rowIdx][columnIdx].text == '') {
        let playerCharacter = ((this.currentPlayer == 0) ? 'X' : 'O');
        this.gameState[rowIdx].splice(columnIdx, 1, {status: '', text: playerCharacter});
        this.currentPlayer = 1 - this.currentPlayer;
        this.turnCount++;
        // this.result = this.endGameMessage[this.getWinner()];
        this.getWinner();
      }
    },
    getWinner() {
      let len = this.gameState.length, state;
      for (let i = 0; i < len; i++) {
        state = this.gameState[i][0].text + this.gameState[i][1].text  + this.gameState[i][2].text ;
        if (state == 'XXX') {
          return this.changeStatusOfWinCells(0, i, 0, i, 1, i, 2);
        }
        if (state == 'OOO') {
          return this.changeStatusOfWinCells(1, i, 0, i, 1, i, 2);
        }

        state = this.gameState[0][i].text  + this.gameState[1][i].text  + this.gameState[2][i].text ;
        if (state == 'XXX') {
          return this.changeStatusOfWinCells(0, 0, i, 1, i, 2, i);
        }
        if (state == 'OOO') {
          return this.changeStatusOfWinCells(1, 0, i, 1, i, 2, i);
        }
      }
      state = this.gameState[0][0].text + this.gameState[1][1].text + this.gameState[2][2].text;
      if (state == 'XXX') {
        return this.changeStatusOfWinCells(0, 0, 0, 1, 1, 2, 2);
      }
      if (state == 'OOO') {
        return this.changeStatusOfWinCells(1, 0, 0, 1, 1, 2, 2);
      }

      state = this.gameState[2][0].text + this.gameState[1][1].text + this.gameState[0][2].text;
      if (state == 'XXX') {
        return this.changeStatusOfWinCells(0, 2, 0, 1, 1, 0, 2);
      }
      if (state == 'OOO') {
        return this.changeStatusOfWinCells(1, 2, 0, 1, 1, 0, 2);
      }

      if (this.turnCount === 9) {
        this.result = this.endGameMessage[2];
      };

    },
    changeStatusOfWinCells(winner, ...arr) {
      this.result = this.endGameMessage[winner];
      for (let i = 0, len = arr.length; i < len; i+=2) {
        let state = this.gameState[arr[i]][arr[i+1]];
        this.gameState[arr[i]].splice(arr[i+1], 1, {status: 'win-cell', text: state.text})
      }
    }
  }
}
</script>

<style scoped>
.player {
  display: inline-block;
  width: 100px;
  margin: 10px;
}
.current_player {
  color: red;
}

.cell {
  width: 10vh;
  height: 10vh;
  border: 1px solid black;
  text-align: center;
}
.cell:hover {
  cursor: pointer;
}
.game-result {
  color: green;
}
.win-cell {
  background: #FFFF00;
}
</style>
