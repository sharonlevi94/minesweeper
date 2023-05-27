<template>
  <div class="minesweeper-wrapper">
    <h1>שולה המוקשים</h1>
    <div class="minesweeper-window">
      <div class="minesweeper-top"></div>
      <div class="minesweeper-body">
        <div v-for="(row, index) in board" :key="index" class="minesweeper-row">
          <div
            v-for="(cell, cellIdx) in row"
            :key="cellIdx"
            :class="['minesweeper-cell', cell.wantToCLick ? 'mousedown' : '']"
            @mousedown.prevent="cell.wantToCLick = true"
            @mouseup="cell.wantToCLick = false">
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
      board: []
    }
  },
  created () {
    this.initBoard()
  },
  methods: {
    initBoard () {
      this.resetBoard()
      this.setBombs()
      this.countBombs()
    },
    resetBoard () {
      for (let rowIdx = 1 ; rowIdx <= 10 ; rowIdx++) {
        const row = []
        for (let colIdx = 0 ; colIdx <= 10; colIdx++) {
          row.push({
            isBomb: false,
            numOfBombsAround: null,
            flag: false,
            wantToCLick: false
          })
        }
        this.board.push(row)
      }
    },
    setBombs () {
      for (let i = 1 ; i <= 10 ; i++) {}
    },
    countBombs () {}
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
    border: 3px solid #8f8f8f;
    border-left-color: #fff;
    border-top-color: #fff;
    display: flex;
    flex-direction: column;
    justify-content: space-between;
    gap: 20px;

    @media screen and (max-width: 800px) {
      width: 90%;
    }

    .minesweeper-top {
      width: 100%;
      height: 20%;
      border: 3px solid #8f8f8f;
      border-right-color: #fff;
      border-bottom-color: #fff;
    }
    .minesweeper-body {
      width: 100%;
      height: 80%;
      border: 3px solid #8f8f8f;
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
          border: 3px solid #8f8f8f;
          border-left-color: #fff;
          border-top-color: #fff;
          cursor: pointer;
          &.mousedown {
            border: unset;
            background-color: #e9e9e9;
          }
        }
      }
    }
  }
}
</style>
