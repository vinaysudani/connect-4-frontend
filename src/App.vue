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
                <div  class="board-box" :style="{ 'background': turn }"></div>
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
            board : [],
            turn : null,
            board_rows : 6,
            board_colls: 7
        }
    },
    created: function() {
        this.initializeBoard()
        this.toggleTurn()
    },
    methods: {
        initializeBoard() {
            for(let i=0; i < this.board_rows; i++) {
                let temp = [];
                for(let j=0; j < this.board_colls; j++) {
                    temp.push(0)
                }
                this.board.push(temp)
            }
        },
        boxClicked(column) {
            if (this.isColumnFillable(column)) {
                this.fillColumn(column)
                this.toggleTurn()
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
                    this.board[row][column] = this.turn
                    break
                }
            }
        },
        isColumnFillable(column) {
            return this.board[0][column] == "" ? true : false
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