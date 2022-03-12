<template>
  <div
    class="game-board"
    :style="{
      gridTemplateColumns: gridTemplateColumns,
      gridTemplateRows: gridTemplateRows,
    }"
  >
    <template v-for="row in gridSize">
      <template v-for="col in gridSize">
        <cell
          :key="'row:' + row + 'col:' + col"
          :value="matrix[row - 1][col - 1].toString()"
        />
      </template>
    </template>
    <div class="lost" v-if="lost">LOST</div>
  </div>
</template>

<script>
import Cell from "./Cell.vue";
const gridSize = 6;
export default {
  data() {
    return {
      matrix: [],
      gridSize: gridSize,
      test: "first",
      lost: false,
    };
  },
  components: {
    Cell,
  },
  computed: {
    gridTemplateColumns() {
      return `repeat(${this.gridSize},${80 / this.gridSize}vmin)`;
    },
    gridTemplateRows() {
      return `repeat(${this.gridSize},${80 / this.gridSize}vmin)`;
    },
  },
  created() {
    this.setUpEvent();
    ///window.addEventListener("keydown", this.handleInputTest);

    let rowRandCell = this.getRandomIndex(),
      colRandCell = this.getRandomIndex(),
      rowRandSecondCell = this.getRandomIndex(),
      colRandSecondCell = this.getRandomIndex();
    if (rowRandCell == rowRandSecondCell && colRandCell == colRandSecondCell) {
      if (rowRandCell < gridSize - 1) {
        rowRandSecondCell = rowRandCell + 1;
      }

      if (rowRandCell == gridSize - 1) {
        rowRandSecondCell = rowRandCell - 1;
      }
    }

    for (let i = 0; i < gridSize; i++) {
      this.matrix[i] = [];
      for (let j = 0; j < gridSize; j++) {
        this.matrix[i][j] = 1;
      }
    }

    this.matrix[rowRandCell][colRandCell] = Math.random() > 0.5 ? 2 : 4;
    this.matrix[rowRandSecondCell][colRandSecondCell] =
      Math.random() > 0.5 ? 2 : 4;
  },
  methods: {
    styleTest() {
      return "";
    },
    setUpEvent() {
      window.addEventListener("keydown", this.handleInput);
    },
    getRandomIndex() {
      return Math.floor(Math.random() * gridSize);
    },
    createMatrix(item) {
      let mat = [];
      for (let row = 0; row < this.gridSize; row++) {
        mat[row] = [];
        for (let col = 0; col < this.gridSize; col++) {
          mat[row][col] = item;
        }
      }
      return mat;
    },
    transpose(matrix) {
      return matrix[0].map((col, c) => matrix.map((row, r) => matrix[r][c]));
    },
    checkWin(matrix) {
      for (let row = 0; row < gridSize; row++) {
        for (let col = 0; col < gridSize; col++) {
          if (matrix[row][col] == 2048) return true;
        }
      }
      return false;
    },
    randomGeneratorCell(matrix) {
      let row = this.getRandomIndex();
      let col = this.getRandomIndex();
      if (matrix[row][col] == 1) {
        matrix[row][col] = Math.random() > 0.5 ? 2 : 4;
        console.log(`Random no in ${row}, ${col}`);
        return matrix;
      } else {
        return this.randomGenerator(matrix);
      }
    },
    randomGenerator(matrix) {
      let indexArr = [];
      for (let row = 0; row < gridSize; row++) {
        for (let col = 0; col < gridSize; col++) {
          if (matrix[row][col] == 1) {
            const rowSize = row * gridSize;
            indexArr.push(rowSize + col);
          }
        }
      }

      let item = indexArr[Math.floor(Math.random() * indexArr.length)];
      let indexX = Math.floor(item / gridSize);
      let indexY = item - indexX * gridSize;
      matrix[indexX][indexY] = Math.random() > 0.5 ? 2 : 4;
      return matrix;
    },
    checkEmptyCell(matrix) {
      for (let row = 0; row < gridSize; row++) {
        for (let col = 0; col < gridSize; col++) {
          if (matrix[row][col] == 1) return true;
        }
      }
      return false;
    },
    handleInputTest(e) {
      console.log(this.matrix);
    },
    handleInput: function (e) {
      switch (e.key) {
        case "ArrowUp":
          this.onKeyPressUp();
          break;
        case "ArrowDown":
          this.onKeyPressDown();
          break;
        case "ArrowLeft":
          this.onKeyPressLeft();
          break;
        case "ArrowRight":
          this.onKeyPressRight();
          break;
        default:
          this.setUpEvent();
          return;
      }
    },
    onKeyPressUp() {
      let matrix = this.matrix;
      let tempMatrix = [];
      for (let row = 0; row < this.gridSize; row++) {
        let col = matrix
          .map((matrixRow) => matrixRow[row])
          .filter((value) => value > 1);
        for (let colIndex = 1; colIndex < col.length; colIndex++) {
          if (col[colIndex] == col[colIndex - 1]) {
            col[colIndex - 1] = col[colIndex] * 2;
            col[colIndex] = 1;
          }
        }
        col = col.filter((value) => value > 1);
        const emptyLength = this.gridSize - col.length;
        for (let res = 0; res < emptyLength; res++) col.push(1);
        tempMatrix[row] = col;
      }

      tempMatrix = this.transpose(tempMatrix);
      this.matrix = tempMatrix;
      if (this.checkWin(this.matrix)) {
        debugger;
        this.matrix = this.createMatrix("🏆");
      } else {
        if (this.checkEmptyCell(this.matrix)) {
          this.matrix = this.randomGenerator(this.matrix);
        } else {
          debugger;
          this.matrix = this.createMatrix("😞");
        }
      }
    },
    onKeyPressDown() {
      let matrix = this.matrix;
      let tempMatrix = [];
      for (let row = 0; row < this.gridSize; row++) {
        let col = matrix
          .map((matrixRow) => matrixRow[row])
          .filter((value) => value > 1);
        for (let colIndex = col.length - 2; colIndex >= 0; colIndex--) {
          if (col[colIndex] == col[colIndex + 1]) {
            col[colIndex + 1] = col[colIndex] * 2;
            col[colIndex] = 1;
          }
        }
        debugger;
        col = col.filter((value) => value > 1);
        const emptyLength = this.gridSize - col.length;
        let tempCol = [];
        for (let res = 0; res < emptyLength; res++) tempCol.push(1);
        col = [...tempCol, ...col];
        tempMatrix[row] = col;
      }

      tempMatrix = this.transpose(tempMatrix);
      this.matrix = tempMatrix;
      if (this.checkEmptyCell(this.matrix)) {
        this.matrix = this.randomGenerator(this.matrix);
      } else {
        debugger;
        this.matrix = this.createMatrix("😞");
      }
    },
    onKeyPressLeft() {
      let matrix = this.matrix;
      let tempMatrix = [];
      for (let row = 0; row < this.gridSize; row++) {
        let rowMatrix = matrix[row].filter((x) => x > 1);
        debugger;
        for (let rowIndex = 1; rowIndex < rowMatrix.length; rowIndex++) {
          if (rowMatrix[rowIndex] == rowMatrix[rowIndex - 1]) {
            rowMatrix[rowIndex - 1] = rowMatrix[rowIndex] * 2;
            rowMatrix[rowIndex] = 1;
          }
        }

        debugger;
        rowMatrix = rowMatrix.filter((value) => value > 1);
        const emptyLength = this.gridSize - rowMatrix.length;
        let tempCol = [];
        for (let res = 0; res < emptyLength; res++) tempCol.push(1);
        rowMatrix = [...rowMatrix, ...tempCol];
        tempMatrix[row] = rowMatrix;
      }

      this.matrix = tempMatrix;
      if (this.checkEmptyCell(this.matrix)) {
        this.matrix = this.randomGenerator(this.matrix);
      } else {
        debugger;
        this.matrix = this.createMatrix("😞");
      }
    },
    onKeyPressRight() {
      let matrix = this.matrix;
      let tempMatrix = [];
      for (let row = 0; row < this.gridSize; row++) {
        let rowMatrix = matrix[row].filter((x) => x > 1);
        debugger;
        for (let rowIndex = rowMatrix.length - 2; rowIndex >= 0; rowIndex--) {
          if (rowMatrix[rowIndex] == rowMatrix[rowIndex + 1]) {
            rowMatrix[rowIndex + 1] = rowMatrix[rowIndex] * 2;
            rowMatrix[rowIndex] = 1;
          }
        }

        debugger;
        rowMatrix = rowMatrix.filter((value) => value > 1);
        const emptyLength = this.gridSize - rowMatrix.length;
        let tempCol = [];
        for (let res = 0; res < emptyLength; res++) tempCol.push(1);
        rowMatrix = [...tempCol, ...rowMatrix];
        tempMatrix[row] = rowMatrix;
      }

      this.matrix = tempMatrix;
      if (this.checkEmptyCell(this.matrix)) {
        this.matrix = this.randomGenerator(this.matrix);
      } else {
        debugger;
        this.matrix = this.createMatrix("😞");
      }
    },
  },
};
</script>

<style lang="css">
@import "../css-variables.css";
.btn-up {
  position: fixed;
  top: 0;
  left: 0;
}

.game-board {
  display: grid;
  /*grid-template-columns: repeat(var(--grid-size), var(--cell-size));
  grid-template-rows: repeat(var(--grid-size), var(--cell-size));
  */
  gap: var(--cell-gap);
  background: var(--bg-color);
  border-radius: 1vmin;
  padding: var(--cell-gap);
  position: relative;
}

.tile {
  --x: 2;
  --y: 1;
  --background-lightness: 80%;
  --text-lightness: 20%;
  position: absolute;
  display: flex;
  justify-content: center;
  align-items: center;
  width: var(--cell-size);
  height: var(--cell-size);
  background-color: red;
  border-radius: 1vmin;
  font-weight: bold;
  top: calc(var(--y) * (var(--cell-size) + var(--cell-gap)) + var(--cell-gap));
  left: calc(var(--x) * (var(--cell-size) + var(--cell-gap)) + var(--cell-gap));
  background-color: hsl(200, 50%, var(--background-lightness));
  color: hsl(200, 25%, var(--text-lightness));
  animation: show 400ms ease-in-out;
  transition: 100ms ease-in-out;
}

@keyframes show {
  0% {
    opacity: 0.5;
    transform: scale(0);
  }
}
</style>
