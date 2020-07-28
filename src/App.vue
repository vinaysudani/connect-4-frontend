<template>
    <div>
        <b-navbar toggleable="lg" type="dark" variant="primary">
            <b-navbar-brand href="#">Connect 4</b-navbar-brand>
        </b-navbar>
        <app-play-local></app-play-local>
    </div>
</template>

<script>
import PlayLocal from './components/PlayLocal.vue'

export default {
    components: {
        'app-play-local': PlayLocal
    }
}
</script>