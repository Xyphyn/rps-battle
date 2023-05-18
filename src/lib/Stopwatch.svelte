<script>
    import { onDestroy } from 'svelte'

    export let start = new Date()
    let elapsed = 0

    function update() {
        elapsed = new Date().getTime() - start.getTime()
    }

    onDestroy(() => {
        clearInterval(interval)
    })

    let interval = setInterval(update, 1000)
</script>

<span>
    {elapsed / 60000}:{(elapsed % 60000) / 1000}
</span>
