<template>
  <div>
    <h1>Vue Сапер</h1>
    <Grid :grid="grid" @open-cell="openCell" @mark-cell="markCell" />

    <div>
      <label for="mines">Количество мин:</label>
      <input type="number" id="mines" v-model="mines" />
    </div>
    <div>
      <label for="rows">Количество строк:</label>
      <input type="number" id="rows" v-model="rows" />
    </div>
    <div>
      <label for="columns">Количество столбиков:</label>
      <input type="number" id="columns" v-model="columns" />
    </div>
    <div>
      <label for="flags">Количество флажков:</label>
      <label>{{ flaggedCells }}</label>
    </div>

    <button @click="restartGame">Рестарт</button>
  </div>
</template>

<script>
import Grid from './components/Grid.vue';

export default {
  components: {
    Grid,
  },
  data() {
    return {
      grid: [],
      rows: 10,
      columns: 10,
      mines: 10,
    };
  },
  computed: {
    flaggedCells() {
      return this.mines - this.grid.flat().filter(cell => cell.marked).length;
    },
    leftMines(){
      return (this.rows * this.columns) - this.grid.flat().filter(cell => cell.open).length;
    }
  },
  watch: {
    mines(newMines) {
      if (newMines <= 0) {
        this.mines = 1;
        alert('Количество мин должно быть больше 0');
      } else if (this.grid.length > 0) {
        const maxMines = this.rows * this.columns - 1;
        if (newMines > maxMines) {
          this.mines = maxMines;
          alert('Количество мин превышает допустимое значение');
        }
      }
    },
    rows(newRows) {
      if (newRows <= 0) {
        this.rows = 1;
        alert('Количество строк должно быть больше 0');
      } else if (this.grid.length > 0) {
        const maxMines = this.rows * this.columns - 1;
        if (this.mines > maxMines) {
          this.mines = maxMines;
          alert('Количество мин превышает допустимое значение');
        }
      }
    },
    columns(newColumns) {
      if (newColumns <= 0) {
        this.columns = 1;
        alert('Количество столбиков должно быть больше 0');
      } else if (this.grid.length > 0) {
        const maxMines = this.rows * this.columns - 1;
        if (this.mines > maxMines) {
          this.mines = maxMines;
          alert('Количество мин превышает допустимое значение');
        }
      }
    },
  },
  mounted() {
    this.restartGame();
  },
  methods: {
    createGrid() {
      for (let row = 0; row < this.rows; row++) {
        this.grid[row] = [];
        for (let col = 0; col < this.columns; col++) {
          this.grid[row][col] = {
            open: false,
            mine: false,
            marked: false,
            surroundingMines: 0,
          };
        }
      }
    },
    plantMines() {
      let plantedMines = 0;
      while (plantedMines < this.mines) {
        const randomRow = Math.floor(Math.random() * this.rows);
        const randomCol = Math.floor(Math.random() * this.columns);
        const cell = this.grid[randomRow][randomCol];
        if (!cell.mine) {
          cell.mine = true;
          plantedMines++;
        }
      }
    },
    calculateSurroundingMines() {
      for (let row = 0; row < this.rows; row++) {
        for (let col = 0; col < this.columns; col++) {
          const cell = this.grid[row][col];
          if (!cell.mine) {
            let count = 0;
            for (let dx = -1; dx <= 1; dx++) {
              for (let dy = -1; dy <= 1; dy++) {
                const newRow = row + dx;
                const newCol = col + dy; if (
                  newRow >= 0 &&
                  newRow < this.rows &&
                  newCol >= 0 &&
                  newCol < this.columns
                ) {
                  if (this.grid[newRow][newCol].mine) {
                    count++;
                  }
                }
              }
            }
            cell.surroundingMines = count;
          }
        }
      }
    },
    openCell(row, col) {
      const cell = this.grid[row][col];
      if (!cell.open && !cell.marked) {
        cell.open = true;
        if (cell.surroundingMines === 0) {
          for (let dx = -1; dx <= 1; dx++) {
            for (let dy = -1; dy <= 1; dy++) {
              const newRow = row + dx;
              const newCol = col + dy;
              if (
                newRow >= 0 &&
                newRow < this.rows &&
                newCol >= 0 &&
                newCol < this.columns
              ) {
                this.openCell(newRow, newCol);
              }
            }
          }
        }
      }
    },
    markCell(row, col) {
      const cell = this.grid[row][col];
      if (!cell.open) {
        cell.marked = !cell.marked;
      }
    },
    restartGame() {
      this.grid = [];
      this.createGrid();
      this.plantMines();
      this.calculateSurroundingMines();
    },
  },
};
</script>