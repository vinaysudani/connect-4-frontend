<template>
  <div>
    <b-navbar toggleable="lg" type="dark" variant="primary">
        <b-navbar-brand href="#">Connect 4</b-navbar-brand>
    </b-navbar>
    <div class="container">
        <div class="row">
            <div class="col-12 mt-2">
                <div v-for="(row, i) of board" :key="i" class="board-row">
                    <div class="board-box" v-for="(box, i) of row" :key="i"
                        @click="boxClicked(i)"
                        :style="{ 'background': box }"
                        ></div>
                </div>
            </div>
            <div class="col-12 mt-2 text-center">
                <div class="board-box" :style="{ 'background': turn }"></div>
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
                let count = 0;
                for (let column = 0; column < this.board_colls; column++) {
                    if (this.board[row][column] == this.turn) {
                       count++
                    } else {
                        count = 0
                    }
                    if (count == 4) {
                        this.gameFinished = true;
                        this.winner =  this.turn;
                        break  
                    }
                }
            }
           
            // Vertial
            // Diagonal
                // One dir
                // Another dir
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
</style>