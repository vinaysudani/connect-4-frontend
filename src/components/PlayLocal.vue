<template>
    <div class="container">
        <div class="row">
            <div class="col-12 mt-2 text-center">
                <div>
                    <div v-if="!gameFinished && turn">Turn of {{ capitalize(turn) }}</div>
                    <div v-if="gameFinished && winner">{{ capitalize(winner) }} won the game</div>
                    <div v-if="gameFinished && !winner">Game draw</div>
                    <div v-if="gameFinished">
                        <button class="btn btn-primary" @click="restartGame">Restart Game</button>
                    </div>
                </div>
            </div>
            <div class="col-12 mt-2">
                <div v-for="(row, i) of board" :key="i" class="board-row">
                    <div class="board-box" v-for="(box, j) of row" :key="j"
                        @click="boxClicked(j)"
                        :class="{ 
                            'box-winning': winningBoxes.includes(boxKey(i, j)),
                            'box-red': box == 'red',
                            'box-blue': box == 'blue'
                        }"
                    ></div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
export default {
    data: function() {
        return {
            board : null,
            turn : null,
            board_rows : 6,
            board_cols: 7,
            gameFinished: false,
            winner: null,
            winningBoxes: [],
            winingBoxesTemp: []
        }
    },
    created: function() {
        this.initializeBoard()
        this.toggleTurn()
    },
    methods: {
        initializeBoard() {
            let board = [];
            for(let i=0; i < this.board_rows; i++) {
                let temp = [];
                for(let j=0; j < this.board_cols; j++) {
                    temp.push(0)
                }
                board.push(temp)
            }
            this.board = board
        },
        boxClicked(column) {
            if (this.isColumnFillable(column) && !this.gameFinished) {
                this.fillColumn(column)
                this.checkForWinner()
                if (!this.gameFinished) {
                    this.checkForGameOver()
                }
                if (!this.gameFinished) {
                    this.toggleTurn()
                }
            }
        },
        toggleTurn() {
            if (this.turn == 'blue') {
                this.turn = 'red'
            } else {
                this.turn = 'blue'
            }
        },
        fillColumn(column) {
            for(let row = this.board_rows -1; row >=0; row--) {
                if (this.board[row][column] == "") {
                    // can not assign value directly like this this.board[row][column] = this.turn
                    // otherwise vue will not detect changes on board and not updated dom
                    this.board[row].splice(column, 1, this.turn) 
                    break
                }
            }
        },
        isColumnFillable(column) {
            return this.board[0][column] == "" ? true : false
        },
        checkForWinner() {
            // Horizonal
            for(let row = this.board_rows - 1; row >=0 && !this.gameFinished; row--)
            {
                this.clearBoxChecking();
                for (let column = 0; column < this.board_cols && !this.gameFinished; column++)
                {
                    this.checkNextBox(row, column)
                }
            }
            // Vertial
            for(let column = 0; column < this.board_cols && !this.gameFinished; column++)
            {
                this.clearBoxChecking();
                for (let row = this.board_rows - 1; row >=0 && !this.gameFinished; row--)
                {
                   this.checkNextBox(row, column)
                }
            }
            // Diagonal (Direction from board bottom-left to board top-right)
            var start_points = [];
            for(let row = 0; row < this.board_rows; row++)
            {
                start_points.push({ row: row, column: 0 })
            }
            for(let column = 1; column < this.board_cols; column++)
            {
                start_points.push({ row: this.board_rows - 1, column: column })
            }
            for(let i = 0; i < start_points.length && !this.gameFinished; i++)
            {
                let row = start_points[i].row;
                let column = start_points[i].column;
                this.clearBoxChecking();
                this.checkNextBox(row, column)
                while(row > 0 && column < this.board_cols - 1 && !this.gameFinished)
                {
                    row--;
                    column++;
                    this.checkNextBox(row, column)
                }
            }
            // Diagonal (Direction from board top-left to board bottom-right)
            start_points = [];
            for(let row = this.board_rows - 1; row >= 0; row--)
            {
                start_points.push({ row: row, column: 0 })
            }
            for(let column = 1; column < this.board_cols; column++)
            {
                start_points.push({ row: 0, column: column })
            }
            for(let i = 0; i < start_points.length && !this.gameFinished; i++)
            {
                let row = start_points[i].row;
                let column = start_points[i].column;
                this.clearBoxChecking();
                this.checkNextBox(row, column)
                while(row < this.board_rows - 1 && column < this.board_cols - 1 && !this.gameFinished)
                {
                    row++;
                    column++;
                    this.checkNextBox(row, column)
                }
            }
        },
        clearBoxChecking(){
            this.winingBoxesTemp = [];
        },
        checkNextBox(row, column){
            if (this.board[row][column] == this.turn) {
                this.winingBoxesTemp.push(this.boxKey(row, column))
            } else {
                this.clearBoxChecking()
            }
            if (this.winingBoxesTemp.length == 4) {
                this.gameFinished = true;
                this.winningBoxes = this.winingBoxesTemp;
                this.winner =  this.turn;  
            }
        },
        checkForGameOver() {
            let emptyBoxExists = false
            this.board.forEach(rows => {
                rows.forEach(boadBox => {
                    if (boadBox == ""){
                        emptyBoxExists = true
                    }
                })
            })
            if (!emptyBoxExists) {
                this.gameFinished = true
            }
        },
        capitalize(s){
            if (typeof s !== 'string') {
                return ''
            }
            return s.charAt(0).toUpperCase() + s.slice(1)
        },
        boxKey(row, column) {
            return row+'-'+column
        },
        restartGame() {
            this.board = null,
            this.turn = null,
            this.gameFinished = false,
            this.winner = null,
            this. winningBoxes = []
            this.initializeBoard()
            this.toggleTurn()
        }
    }
}
</script>

<style scoped>
    .board-row {
        width: 100%;
        text-align: center;
    }
    .board-box {
        width: 60px;
        height: 60px;
        border: 1px solid blue;
        display: inline-block;
        margin: 5px;
        border-radius: 100%;
    }
    .board-box.box-red {
        background: red;
    }
    .board-box.box-blue {
        background: blue;
    }
    .board-box.box-winning {
        border: 3.5px solid yellow;
    }
</style>