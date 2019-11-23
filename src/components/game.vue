<template>
  <div style="width: 500px; margin: auto">
    <!-- header -->
    <div style="color: gray; width: 512px;">
      <div class="space-between" style="margin:70px 0">
        <div>
          <h1 style="font-size: 2.5em;">2048</h1>
          <p class="game-intro">Join the numbers and get to the <strong>2048 tile!</strong></p>
        </div>
        <div class="flex-center">
          <div class="blue-container" style="padding: 5px 15px; margin-right: 10px;">
            <p>score</p>
            <h2>{{ score }}</h2>
          </div>
          <div class="blue-container" style="padding: 5px 15px;">
            <p>best</p>
            <h2>{{ best }}</h2>
          </div>
        </div>
      </div>
      <div>
        <p class="button bold" style="margin-bottom: 40px" @click="init"><i class="icon">add_circle</i> New Game</p>

        <select v-model="cellRow" style="margin-left: 20px;outline: none;border: none;background: none;">
          <option value="3">3</option>
          <option value="4">4</option>
          <option value="5">5</option>
        </select>
        cells
      </div>
    </div>

    <div id="game" class="flex-center" style="width: 500px; height: 500px; flex-wrap: wrap; border: 6px solid lightgray; border-radius: 10px">
      <span>0</span>
      <span>0</span>
      <span>0</span>
      <span>0</span>
      <span>0</span>
      <span>0</span>
      <span>0</span>
      <span>0</span>
      <span>0</span>
      <span class="active" style="background: green">0</span>
      <span>0</span>
      <span>0</span>
      <span>0</span>
      <span>0</span>
      <span>0</span>
      <span>0</span>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      cellRow: 4,
      score: 0,
      best: 0,
      cellVal: []
    };
  },
  methods: {
    init() {
      this.cellVal = [];
      this.game();
    },
    game() {
      var newNum = this.randN();
      var gameContainer = document.getElementById("game");
      var cells = this.cellRow * this.cellRow;
      var cellSize = 100 / this.cellRow;
      gameContainer.innerHTML = "";
      var evt = false;

    if(!evt){
      ["ArrowUp", "ArrowDown", "ArrowLeft", "ArrowRight"].forEach(e => {
              document.body.addEventListener("keyup", () => { //!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
                if (event.key === e) {
                  evt = true;
                  for (let i = 0; i < cells; i++) {
                    var cellId = `${Math.ceil((i + 1) / this.cellRow)}.${(i + 1) % this.cellRow || this.cellRow}`;
                    i + 1 === newNum ? (this.cellVal[i] = 2) : (this.cellVal[i] = 0);
                    gameContainer.appendChild(this.cell(cellSize, cellId, this.cellVal[i]));
                  }
                  console.log(this.cellVal, newNum, e);
                }
              },{once: true});
            });




    }
      
    },
    cell(cellSize, cellId, cellVal) {
      var cell = document.createElement("SPAN");
      cell.id = cellId;
      cell.style = `width: ${cellSize}%; height: ${cellSize}%`;
      cellVal !== 0 ? ((cell.style.background = `hsl(${(360 / 100) * cellVal}, 100%, 30%)`), (cell.style.color = "white")) : ((cell.style.background = "none"), (cell.style.color = "none"));
      cell.innerHTML = cellVal;
      return cell;
    },
    randN() {
      return Math.floor(Math.random() * (this.cellRow * this.cellRow)) + 1;
    }
  },
  mounted() {
    this.init();
  }
};
</script>
