<template>
    <div>
        <b-navbar toggleable="lg" type="dark" variant="primary">
            <b-navbar-brand href="#">Connect 4</b-navbar-brand>
        </b-navbar>
        <div class="container">
            <div class="row">
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
                <div class="col-12 mt-2 text-center">
                    <div class="board-box" :style="{ 'background': turn }"></div>
                    <div v-if="!gameFinished && turn">Turn of {{ capitalize(turn) }}</div>
                    <div v-if="gameFinished && winner">{{ capitalize(winner) }} won the game</div>
                    <div v-if="gameFinished && !winner">Game Draw</div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
export default {
    name: 'App',
    data: function() {
        return {
            board : null,
            turn : null,
            board_rows : 6,
            board_colls: 7,
            gameFinished: false,
            winner: null,
            winningBoxes: []
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
                for(let j=0; j < this.board_colls; j++) {
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
            
            console.log('check for winner')
            
            // Horizonal
            for(let row = this.board_rows -1; row >=0 && !this.gameFinished; row--) {
                let winningBoxes = [];
                for (let column = 0; column < this.board_colls; column++) {
                    if (this.board[row][column] == this.turn) {
                       winningBoxes.push(this.boxKey(row, column))
                    } else {
                        winningBoxes = [];
                    }
                    if (winningBoxes.length == 4) {
                        this.gameFinished = true;
                        this.winningBoxes = winningBoxes;
                        this.winner =  this.turn;
                        break  
                    }
                }
            }
           
            // Vertial
            // Diagonal
                // One dir
                // Another dir
        },
        capitalize(s){
            if (typeof s !== 'string') {
                return ''
            }
            return s.charAt(0).toUpperCase() + s.slice(1)
        },
        boxKey(row, column) {
            return row+'-'+column
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
    .board-box.box-red.box-winning {
        background: yellow;
        border: 3px solid red;
    }
    .board-box.box-blue.box-winning {
        background: yellow;
        border: 3px solid blue;
    }
</style>