<template>
  <div class="minesweeper-wrapper">
    <h1>Minesweeper Online</h1>
    <h6>Powered By Sharon Levi</h6>
    <div class="minesweeper-window">
      <div class="minesweeper-top">
        <div class="reset-btn" @click="initBoard">
          <i v-if="!gameOver" class="icon playing"/>
          <i v-else class="icon game-over"/>
        </div>
      </div>
      <div class="minesweeper-body">
        <div v-for="(row, index) in board" :key="index" class="minesweeper-row">
          <div
            v-for="(cell, cellIdx) in row"
            :key="cellIdx"
            :class="['minesweeper-cell', cell.wantToCLick || cell.exposed ? 'mousedown' : '', gameOver && cell.isBomb && cell.exposed ? 'game-over' : '']"
            @click="!cell.flag && !gameOver ? handleClick(index, cellIdx) : null"
            @mousedown.prevent="cell.wantToCLick = true"
            @mouseup="cell.wantToCLick = false"
            @contextmenu.prevent="cell.flag = !cell.flag">
            <i v-if="gameOver && cell.isBomb && !cell.flag" class="icon bomb"/>
            <i v-if="cell.flag" class="icon flag"/>
            <span v-if="cell && cell.numOfBombsAround > 0 && cell.exposed && !cell.isBomb">
              {{ cell && cell.numOfBombsAround }}
            </span>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'IndexPage',
  data () {
    return {
      board: [],
      gameOver: false
    }
  },
  created () {
    this.initBoard()
  },
  methods: {
    initBoard () {
      this.board = []
      this.gameOver = false
      this.resetBoard()
      this.setBombs()
      this.countBombs()
    },
    resetBoard () {
      for (let rowIdx = 1 ; rowIdx <= 10 ; rowIdx++) {
        const row = []
        for (let colIdx = 1 ; colIdx <= 10; colIdx++) {
          row.push({
            isBomb: false,
            numOfBombsAround: null,
            flag: false,
            wantToCLick: false,
            exposed: false
          })
        }
        this.board.push(row)
      }
    },
    setBombs () {
      for (let i = 1 ; i <= 20 ; i++) {
        let row = Math.floor(Math.random() * 10),
        cell = Math.floor(Math.random() * 10);
        // console.log(`(${row},${cell})`)
        while (this.board[row][cell].isBomb) {
            row = Math.floor(Math.random() * 10);
            cell = Math.floor(Math.random() * 10);
        }
        this.board[row][cell].isBomb = true
      }
    },
    countBombs () {
      for (let rowIdx = 0 ; rowIdx < 10 ; rowIdx++) {
        for (let colIdx = 0 ; colIdx < 10; colIdx++) {
          this.board[rowIdx][colIdx].numOfBombsAround = this.countBombsAround(rowIdx, colIdx)
        }
      }
    },
    countBombsAround (row, cell) {
      let bombs = 0;
      if (this.board[row-1]?.[cell]?.isBomb) {
        bombs += 1
      }
      if (this.board[row-1]?.[cell-1]?.isBomb) {
        bombs += 1
      }
      if (this.board[row-1]?.[cell+1]?.isBomb) {
        bombs += 1
      }
      if (this.board[row]?.[cell-1]?.isBomb) {
        bombs += 1
      }
      if (this.board[row]?.[cell+1]?.isBomb) {
        bombs += 1
      }
      if (this.board[row+1]?.[cell-1]?.isBomb) {
        bombs += 1
      }
      if (this.board[row+1]?.[cell+1]?.isBomb) {
        bombs += 1
      }
      if (this.board[row+1]?.[cell]?.isBomb) {
        bombs += 1
      }
      return bombs
    },
    handleClick (row, cell) {
      if (this.board[row][cell].exposed || this.board[row][cell].flag) {
        return
      }
      if (this.board[row][cell].isBomb) {
        this.board[row][cell].exposed = true
        this.gameOver = true
        return
      }
      if (this.board[row][cell].numOfBombsAround >= 0 &&
        !this.board[row][cell].flag) {
        this.emptyCellExposed(row, cell)
      }
    },
    emptyCellExposed (row, cell) {
      // Stop condition 1:
      if (!this.board[row]?.[cell]) {
        return
      }
      // Stop condition 2:
      if ((this.board[row][cell].isBomb ||
      this.board[row][cell].numOfBombsAround > 0 ||
      this.board[row][cell].flag) &&
        !this.board[row][cell].exposed
      ) {
        if (this.board[row][cell].numOfBombsAround > 0) {
          this.board[row][cell].exposed = true
        }
        return
      }

      if (!this.board[row][cell]?.isBomb &&
        !this.board[row][cell].flag &&
        this.board[row][cell].numOfBombsAround === 0 &&
      !this.board[row][cell].exposed) {
        this.board[row][cell].exposed = true
        this.emptyCellExposed(row-1, cell)
        this.emptyCellExposed(row-1, cell-1)
        this.emptyCellExposed(row-1, cell+1)
        this.emptyCellExposed(row, cell-1)
        this.emptyCellExposed(row, cell+1)
        this.emptyCellExposed(row+1, cell)
        this.emptyCellExposed(row+1, cell+1)
        this.emptyCellExposed(row+1, cell-1)
      }
    }
  }
}
</script>

<style lang="scss" scoped>
.minesweeper-wrapper {
  width: 100vw;
  height: 100vh;
  background-color: #e9e9e9;
  color: #5a5a5a;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;

  .minesweeper-window {
    width: 30%;
    height: 70vh;
    background-color: #eaeaea;
    padding: 10px;
    border: 3px solid $border;
    border-left-color: #fff;
    border-top-color: #fff;
    display: flex;
    flex-direction: column;
    justify-content: space-between;
    gap: 20px;

    @media screen and (max-width: 800px) {
      width: 90%;
      min-height: 50vh;
    }

    .minesweeper-top {
      width: 100%;
      height: 20%;
      border: 3px solid $border;
      border-right-color: #fff;
      border-bottom-color: #fff;
      display: flex;
      justify-content: center;
      align-items: center;
      .reset-btn {
        border: 3px solid $border;
        border-right-color: #fff;
        border-bottom-color: #fff;
        width: 60px;
        height: 90%;
        padding: 3px;
        display: flex;
        justify-content: center;
        align-items: center;
      }
      @media screen and (max-width: 800px) {
        min-height: 10%;
      }
    }
    .minesweeper-body {
      width: 100%;
      height: 80%;
      border: 3px solid $border;
      border-right-color: #fff;
      border-bottom-color: #fff;
      display: flex;
      flex-direction: column;
      .minesweeper-row {
        display: flex;
        width: 100%;
        height: 10%;
        .minesweeper-cell {
          width: 10%;
          height: 100%;
          border: 3px solid $border;
          border-left-color: #fff;
          border-top-color: #fff;
          cursor: pointer;
          display: flex;
          justify-content: center;
          align-items: center;
          &.mousedown {
            border: unset;
            background-color: #e9e9e9;
          }
          &.game-over {
            border: unset;
            background-color: red;
            border: 1px solid $border;
          }
        }
      }

      @media screen and (max-width: 800px) {
        height: 90%;
      }
    }
  }
}
</style>
