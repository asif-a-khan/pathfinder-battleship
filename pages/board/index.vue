<template>
  <v-container fluid class="pa-0 ma-0">
    <v-row class="h-screen pa-0 ma-0">
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
  </v-container>
</template>

<script setup lang="ts">
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
  }

  generateBoard()

  const currentCell = ref<Cell>({
    value: 0,
    row: 0,
    col: 0,
    color: ''
  })

  const handleClick = (cell: Cell) => {
    cell.color = 'red'
    currentCell.value = cell
  }

  

  const handleKeydown = (event: KeyboardEvent) => {
    if (event.key === 'ArrowUp') {
      for (let i = currentCell.value.row; i >= 0; i--) {
        setTimeout(() => {
          board.value[currentCell.value.row - i - 1][currentCell.value.col].color = 'blue'
        }, i * 100);
      }
    }
    if (event.key === 'ArrowDown') {
      for (let i = 0; i < 15; i++) {
        setTimeout(() => {
          board.value[currentCell.value.row + i + 1][currentCell.value.col].color = 'blue'
        }, i * 100);
      }
    }
    if (event.key === 'ArrowLeft') {
      for (let i = currentCell.value.col; i >= 0; i--) {
        setTimeout(() => {
          board.value[currentCell.value.row][currentCell.value.col - i - 1].color = 'blue'
        }, i * 100);
      }
    }
    if (event.key === 'ArrowRight') {
      for (let i = 0; i < 15; i++) {
        setTimeout(() => {
          board.value[currentCell.value.row][currentCell.value.col + i + 1].color = 'blue'
        }, i * 100);
      }
    }
  }

  watch(currentCell, (newVal, oldVal) => {
    oldVal.color = ''
    console.log("New",newVal.color)
  })

  onMounted(() => {
    window.addEventListener('keydown', handleKeydown) 
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