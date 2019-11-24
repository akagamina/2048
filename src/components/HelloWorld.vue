<template>
  <div class="home-page">
    <div class="info-container">
      <h1>2048'e Hoşgeldin</h1>
      <p class="info-item">Tek yapman gereken yön tuşlarını kullanmak.</p>
      <hr>
      <div class="score">
        <span>
          Skorun: <h3>{{score}}</h3>
        </span>
      </div>
      <div class="dab">
        <span>
          Tık: <h3>{{dab}}</h3>
        </span>
      </div>
    </div>
    <div class="grid-container">
      <div class="grid-block">
        <div class="grid-row"
             v-for="items in table">
          <div class="grid-item"
               v-for="(item, index) in items"
               :key="index"
               :class="{'bg-2': item === 2, 'bg-4': item === 4, 'bg-8' : item === 8, 
                        'bg-16' : item === 16, 'bg-32' : item === 32, 'bg-64' : item === 64, 
                        'bg-128' : item === 128, 'bg-256' : item === 256, 'bg-512' : item === 512, 
                        'bg-1024' : item === 1024, 'win-2048' : item === 2048}">
            <p v-if="item === 0"></p>
            <p v-else>{{item}}</p>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      up: 38,
      right: 39,
      down: 40,
      left: 37,
      table: [[0, 0, 0, 0], [0, 0, 0, 0], [0, 0, 0, 0], [0, 0, 0, 0]],
      exTable: [0, 0],
      score: 0,
      dab: 0
    };
  },
  created() {
    document.addEventListener("keyup", this.handleArrowKeys);
    this.randomNumbers(this.table);
    this.randomNumbers(this.table);
  },
  destroyed() {
    document.removeEventListener("keyup", this.handleArrowKeys);
  },
  methods: {
    handleArrowKeys(e) {
      let pastCell = new this.getTable(this.table);
      let key = e.which || e.keyCode;
      switch (key) {
        case this.up:
          this.actUp();
          break;
        case this.right:
          this.actRight();
          break;
        case this.down:
          this.actDown();
          break;
        case this.left:
          this.actLeft();
          break;
      }
      this.dab++;
      if (this.compareCells(pastCell, this.table)) {
        this.table = this.randomNumbers(this.table);
      }
    },
    actUp() {
      this.table = this.getTable(this.table);
      this.table = this.rollInsideArray(this.table, "left");
    },
    actRight() {
      this.table = this.rollInsideArray(this.table, "right");
    },
    actDown() {
      this.table = this.getTable(this.table);
      this.table = this.rollInsideArray(this.table, "right");
    },
    actLeft() {
      this.table = this.rollInsideArray(this.table, "left");
    },
    setScore(scr) {
      this.score += scr;
    },
    randomNumbers(tables) {
      let emptyCells = [];
      for (let i = 0; i < tables.length; i++) {
        for (let j = 0; j < tables[i].length; j++) {
          if (tables[i][j] === 0) {
            emptyCells.push({
              x: i,
              y: j
            });
          }
        }
      }
      if (emptyCells.length > 0) {
        let random = Math.floor(Math.random() * emptyCells.length);
        tables[emptyCells[random].x][emptyCells[random].y] =
          Math.random() > 0.5 ? 2 : 4;
        this.exTable = [emptyCells[random].x, emptyCells[random].y];
      }
      return tables;
    },
    rollInsideArray(tables, direction) {
      var getTable = this.getTable(tables);
      for (let k = 0; k < getTable.length; k++) {
        var arrLength = getTable[k].length;
        getTable[k] = getTable[k].filter(filt => filt);
        getTable[k] = this.mergeValues(getTable[k], direction);
        getTable[k] = getTable[k].filter(filt => filt);
        var finalLength = getTable[k].length;
        for (let i = 0; i < arrLength - finalLength; i++) {
          if (direction == "left") {
            getTable[k].push(0);
          } else {
            getTable[k].unshift(0);
          }
        }
      }
      return getTable;
    },
    mergeValues(cellArr, direction) {
      if (direction == "left") {
        for (let i = 0; i < cellArr.length - 1; i++) {
          if (cellArr[i] !== 0 && cellArr[i] === cellArr[i + 1]) {
            cellArr[i] = cellArr[i] * 2;
            cellArr[i + 1] = 0;
            this.setScore(cellArr[i]);
          }
        }
      } else if (direction == "right") {
        for (let i = cellArr.length - 1; i > -1; i--) {
          if (cellArr[i] !== 0 && cellArr[i] === cellArr[i - 1]) {
            cellArr[i] = cellArr[i] * 2;
            cellArr[i - 1] = 0;
            this.setScore(cellArr[i]);
          }
        }
      }
      return cellArr;
    },
    getTable(tables) {
      let tableArr = [[], [], [], []];
      for (let i = 0; i < tables.length; i++) {
        for (let j = tables[i].length - 1; j > -1; j--) {
          tableArr[j][i] = tables[i][j];
        }
      }
      return tableArr;
    },
    compareCells(pastCell, currentCell) {
      for (let i = 0; i < pastCell.length; i++) {
        for (let j = 0; j < currentCell.length; j++) {
          if (pastCell[i][j] !== currentCell[i][j]) return true;
        }
      }
    }
  }
};
</script>

<style scoped lang="scss">
.info-container {
  text-align: left;
  margin-bottom: 30px;
  .info-item {
    margin-bottom: 5px;
  }
  .score {
    h3 {
      margin: 0;
      display: inline-block;
      font-weight: bold;
    }
  }
  .dab {
    h3 {
      display: inline-block;
      margin: 0;
      font-weight: bold;
    }
  }
}
.grid-container {
  width: 420px;
  height: 380px;
  background: #887662;
  border-radius: 8px;
  display: flex;
  justify-content: center;
  align-items: center;
  margin: 0 auto;
  .grid-block {
    display: inline-flex;
    justify-content: center;
    align-items: center;
    .grid-item {
      width: 90px;
      height: 85px;
      display: flex;
      justify-content: center;
      align-items: center;
      border-radius: 4px;
      margin: 5px;
      background: whitesmoke;
      color: midnightblue;
      border: 1px solid #b79999;
      box-shadow: 1px 1px 4px black;
    }
  }
}
.bg-2 {
  background-color: blanchedalmond !important;
}
.bg-4 {
  background-color: #f7dcb3 !important;
}
.bg-8 {
  background-color: #d8b888 !important;
}
.bg-16 {
  background-color: #a78d66 !important;
}
.bg-32 {
  background-color: #8c6d40 !important;
}
.bg-64 {
  background-color: #a0712d !important;
}
.bg-128 {
  background-color: #ea8c03 !important;
}
.bg-256 {
  background-color: #9a5b00 !important;
}
.bg-512 {
  background-color: #7b4901 !important;
}

.bg-1024 {
  background-color: #ffb64c !important;
}

.win-2048 {
  animation-name: stretch2048;
  animation-duration: 1.5s;
  animation-timing-function: ease-out;
  animation-delay: 0;
  animation-direction: alternate;
  animation-iteration-count: infinite;
  animation-fill-mode: none;
  animation-play-state: running;
}

@keyframes stretch2048 {
  0% {
    transform: scale(0.9);
    background-color: rgb(114, 47, 83);
  }

  100% {
    transform: scale(1);
  }
}
</style>

