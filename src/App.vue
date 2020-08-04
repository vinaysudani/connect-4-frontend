<template>
    <div>
        <b-navbar toggleable="lg" type="dark" variant="primary">
            <b-navbar-brand href="#">Connect 4</b-navbar-brand>
        </b-navbar>
        <app-game-board></app-game-board>
    </div>
</template>

<script>
import GameBoard from './components/GameBoard.vue'

export default {
    components: {
        'app-game-board': GameBoard
    }
}
</script>