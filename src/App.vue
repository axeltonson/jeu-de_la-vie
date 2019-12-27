<template>
  <div id="app">
    <h1>Le jeu de la vie <small>v0.1</small></h1>
    <h3></h3>
    <div class="wrapper" v-if="ready">
      <div class="row" v-for="(row, index) in board.cells" :key="row + index">
        <div class="cell" v-for="(cell, index) in row" :key="cell + index" :class="cell.state ? 'alive': 'dead'"></div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'app',

  data () {
    return {
      ready: false,
      board: null
    }
  },

  mounted () {
    this.board = new Board(50, 80)
    setInterval(this.board.play, 200)
  },

  watch: {
    board (newV, oldV) {
      this.ready = true
    }
  }
}

class Board {
  row = 0
  column = 0
  cells = []

  constructor (row, column) {
    this.row = row
    this.column = column

    for (let rowIndex = 0; rowIndex < this.row; rowIndex++) {
      let newRow = []
      for (let colIndex = 0; colIndex < this.column; colIndex++) {
        const params = {
          posX: colIndex,
          posY: rowIndex
        }
        newRow.push(new Cell(params))
      }
      this.cells.push(newRow)
    }
  }

  play = () => {
    let computedCells = []

    for (let rowIndex = 0; rowIndex < this.cells.length; rowIndex++) {
      let computedRow = []

      for (let colIndex = 0; colIndex < this.cells[rowIndex].length; colIndex++) {
        let nbCellulesAutour = 0

        if (this.cells[rowIndex][colIndex + 1] && this.cells[rowIndex][colIndex + 1].state === true) nbCellulesAutour++

        if (this.cells[rowIndex][colIndex - 1] && this.cells[rowIndex][colIndex - 1].state === true) nbCellulesAutour++

        if (this.cells[rowIndex + 1] && this.cells[rowIndex + 1][colIndex + 1] && this.cells[rowIndex + 1][colIndex + 1].state === true) nbCellulesAutour++

        if (this.cells[rowIndex + 1] && this.cells[rowIndex + 1][colIndex] && this.cells[rowIndex + 1][colIndex].state === true) nbCellulesAutour++

        if (this.cells[rowIndex + 1] && this.cells[rowIndex + 1][colIndex - 1] && this.cells[rowIndex + 1][colIndex - 1].state === true) nbCellulesAutour++

        if (this.cells[rowIndex - 1] && this.cells[rowIndex - 1][colIndex - 1] && this.cells[rowIndex - 1][colIndex - 1].state === true) nbCellulesAutour++

        if (this.cells[rowIndex - 1] && this.cells[rowIndex - 1][colIndex] && this.cells[rowIndex - 1][colIndex].state === true) nbCellulesAutour++

        if (this.cells[rowIndex - 1] && this.cells[rowIndex - 1][colIndex + 1] && this.cells[rowIndex - 1][colIndex + 1].state === true) nbCellulesAutour++

        if (this.cells[rowIndex][colIndex].state === true) {
          if (nbCellulesAutour === 3 || nbCellulesAutour === 2) {
            computedRow.push(true)
          } else {
            computedRow.push(false)
          }
        } else {
          if (nbCellulesAutour === 3) {
            computedRow.push(true)
          } else {
            computedRow.push(false)
          }
        }
      }

      computedCells.push(computedRow)
    }

    // Application des états
    computedCells.forEach((row, rowIndex) => {
      row.forEach((cell, colIndex) => {
        this.cells[rowIndex][colIndex].setState(cell)
      })
    })
  }
}

class Cell {
  posX = 0
  posY = 0
  state = false

  constructor (params) {
    this.posX = params.posX
    this.posY = params.posY
    this.state = this.randomState()
  }

  randomState () {
    let randomNumber = Math.floor(Math.random() * (10 - 0) + 0)
    return randomNumber < 5
  }

  setState (state) {
    if (state !== undefined) this.state = state
    else this.state = this.randomState()
  }
}
</script>

<style lang="scss" scoped>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
}

h1 {
  text-transform: uppercase;
  small {
    text-transform: none;
    font-size: 12px;
  }
}

.row {
  display: flex;
}

.cell {
  width: 7px;
  height: 7px;
  border: 1px solid lightblue;
  margin: 1px;
  display: flex;
  justify-content: center;
  align-items: center;
  transition: background 0.15s;

  &.alive {
    background: black;
  }

  &.dead {
    background: #fff;
  }
}
</style>
