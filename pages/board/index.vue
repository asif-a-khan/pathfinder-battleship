<template>
  <v-container fluid class="pa-0 ma-0">
    <v-row class="d-flex justify-center mt-4">
      <v-col cols="3" class="d-flex justify-center">
        <h4>Range: {{ range }}</h4>
      </v-col>
      <v-col cols="3" class="d-flex justify-center">
        <h4>Score: {{ score }}</h4>
      </v-col>
      <v-col cols="3" class="d-flex justify-center flex-column">
        <h4>Current Moves: {{ currentRoundMoves }}</h4>
        <h4>Total Moves: {{ totalMoves }}</h4>
      </v-col>
    </v-row>
    <v-row class="h-screen">
      <v-col 
        cols="12"
        class="d-flex flex-column align-center justify-center"
      >
        <div v-for="row, rowIndex in board" :key="rowIndex">
          <div v-for="cell, cellIndex in row" :key="cellIndex" style="display: inline;">
            <v-btn 
              :height="cellSize" 
              :width="cellSize"
              rounded="0"
              elevation="0"
              variant="tonal"
              style="border: solid 1px black;"
              :color="cell.color"
              @click="(event: MouseEvent) => handleClick(cell)"
            >
              
            </v-btn>
          </div>
        </div>
      </v-col>
    </v-row>
    <v-snackbar
      v-model="snackbar"
      :color="snackbarColor"
      :timeout="750"
      location="top"
    >
      {{ snackbarText }}
    </v-snackbar>
  </v-container>
</template>

<script setup lang="ts">
  // Snackbar state and methods
  const snackbar = ref(false)
  const snackbarColor = ref('')
  const snackbarText = ref('')

  const setSnackbar = (color: string, text: string) => {
    snackbarColor.value = color
    snackbarText.value = text
    snackbar.value = true
  }
  // Board State & Types
  type Cell = {
    value: number
    row: number
    col: number
    color: string
  }
  type Board = Cell[][]
  const board = ref<Board>([])
  const boardSize = ref(15)
  const cellSize = ref(50)

  // Rules
  const moveCounter = ref(0)

  // Player Stats
  const range = ref(1)
  const score = ref(0)
  const totalMoves = ref(0)
  const currentRoundMoves = ref(0)

  // Current cell and target cell
  const currentCell = ref<Cell>({
    value: 0,
    row: 0,
    col: 0,
    color: 'red'
  })

  const targetCell = ref<Cell>({
    value: 0,
    // Random number between 0 and boardSize
    row: Math.floor(Math.random() * boardSize.value),
    col: Math.floor(Math.random() * boardSize.value),
    color: ''
  })

  // Init & Reset
  const generateBoard = () => {
    // Create a board of size boardSize x boardSize where all cells have values equal to their index
    for (let i = 0; i < boardSize.value; i++) {
      board.value[i] = []
      for (let j = 0; j < boardSize.value; j++) {
        board.value[i][j] = {
          value: i * boardSize.value + j,
          row: i,
          col: j,
          color: ''
        }
      }
    }

    board.value[currentCell.value.row][currentCell.value.col].color = 'red'
  }

  generateBoard()

  const resetBoard = () => {
    targetCell.value.row = Math.floor(Math.random() * boardSize.value)
    targetCell.value.col = Math.floor(Math.random() * boardSize.value)
    moveCounter.value = 0
    currentRoundMoves.value = 0

    setTimeout(() => {
      generateBoard()
    }, 1500);
  }

  const win = () => {
    board.value[targetCell.value.row][targetCell.value.col].color = 'green'
    score.value += 1
    setSnackbar('success', 'You hit the battleship!')
    resetBoard()
  }
  

  const handleClick = (cell: Cell) => {
    // If you click on the target you win
    if (cell.row === targetCell.value.row && cell.col === targetCell.value.col) {
      win()
    }
    // If the cell is already red, return
    if (cell.color === 'red') {
      setSnackbar('error', 'You have already clicked on this cell')
      return
    }
    // If the cell is more than 1 away from the current cell, return
    if (Math.abs(cell.row - currentCell.value.row) > range.value || Math.abs(cell.col - currentCell.value.col) > range.value) {
      setSnackbar('error', 'Too far from current position')
      return
    }
    currentCell.value = board.value[cell.row][cell.col]
    generateBoard()
    moveCounter.value = 0
  }

  const handleKeydown = (event: KeyboardEvent) => {
    if (moveCounter.value === 1) {
      totalMoves.value++
      currentRoundMoves.value++
      if (event.key === 'w') {
        // Check if it is possible to go up one cell and if it is call handleclick with that cell
        if (currentCell.value.row !== 0) {
          const newCell = board.value[currentCell.value.row - 1][currentCell.value.col]
          handleClick(newCell)
        } else {
          setSnackbar('error', 'Out of bounds')
        }
      } else if (event.key === 's') {
        // Check if it is possible to go up one cell and if it is call handleclick with that cell
        if (currentCell.value.row !== boardSize.value - 1) {
          const newCell = board.value[currentCell.value.row + 1][currentCell.value.col]
          handleClick(newCell)
        } else {
          setSnackbar('error', 'Out of bounds')
        }
      } else if (event.key === 'a') {
        // Check if it is possible to go up one cell and if it is call handleclick with that cell
        if (currentCell.value.col !== 0) {
          const newCell = board.value[currentCell.value.row][currentCell.value.col - 1]
          handleClick(newCell)
        } else {
          setSnackbar('error', 'Out of bounds')
        }
      } else if (event.key === 'd') {
        // Check if it is possible to go up one cell and if it is call handleclick with that cell
        if (currentCell.value.col !== boardSize.value - 1) {
          const newCell = board.value[currentCell.value.row][currentCell.value.col + 1]
          handleClick(newCell)
        } else {
          setSnackbar('error', 'Out of bounds')
        }
      }
    } else {

      if (event.key === 'w') {
        event.preventDefault()
        // Change the color of all cells above the current cell to blue in sequence one after the other
        for (let i = currentCell.value.row - 1; i >= currentCell.value.row - range.value; i--) {
          // If the target cell is in the way then do not change any tiles above the target cell
          if (i === targetCell.value.row && targetCell.value.col === currentCell.value.col) {
            win()
            break
          } else {
            // Change the colors of the tiles in sequence one after the other so it looks animated
            setTimeout(() => {
              board.value[i][currentCell.value.col].color = 'blue'
            }, i / 200)
          }
        }
      } else if (event.key === 's') {
        event.preventDefault()
        for (let i = currentCell.value.row + 1; i <= currentCell.value.row + range.value; i++) {
          if (i === targetCell.value.row && targetCell.value.col === currentCell.value.col) {
            win()
            break
          } else {
            setTimeout(() => {
              board.value[i][currentCell.value.col].color = 'blue'
            }, i / 200)
          }
        }
      } else if (event.key === 'a') {
        for (let i = currentCell.value.col - 1; i >= currentCell.value.col - range.value; i--) {
          if (i === targetCell.value.col && targetCell.value.row === currentCell.value.row) {
            win()
            break
          } else {
            setTimeout(() => {
              board.value[currentCell.value.row][i].color = 'blue'
            }, i / 200)
          }
        }
      } else if (event.key === 'd') {
        for (let i = currentCell.value.col + 1; i <= currentCell.value.col + range.value; i++) {
          if (i === targetCell.value.col && targetCell.value.row === currentCell.value.row) {
            win()
            break
          } else {
            setTimeout(() => {
              board.value[currentCell.value.row][i].color = 'blue'
            }, i / 200)
          }
        } 
      }
      moveCounter.value++
    }
  }

  watch(currentCell, (newVal, oldVal) => {
    oldVal.color = ''
    console.log("New",newVal.color)
  })

  onMounted(() => {
    window.addEventListener('keydown', handleKeydown)
    generateBoard()
  })


</script>

<style>
  #board {
    border: 1px solid black
  }
  #board:hover {
    cursor: pointer
  }

</style>