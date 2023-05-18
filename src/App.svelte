<script lang="ts">
    import Game from './Game.svelte'
</script>

<main class="container">
    <Game agentCount={200} />
</main>

<style>
    .container {
        width: 100%;
        display: flex;
        flex-direction: column;
        align-items: center;
    }
</style>
