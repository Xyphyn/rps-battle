<script>
    import RangeInput from '$lib/ui/input/RangeInput.svelte'
    import TextInput from '$lib/ui/input/TextInput.svelte'
    import Button from '../lib/ui/Button.svelte'
    import { Color } from '../lib/ui/colors'
    import Game from './Game.svelte'

    let settings = {
        battleName: '',
        stepsPerSecond: 5,
        width: '640',
        height: '480',
    }

    let gameRunning = false
</script>

{#if !gameRunning}
    <form
        class="flex flex-col gap-8 p-8 mx-auto max-w-xl rounded-md shadow-md bg-zinc-900"
        on:submit|preventDefault={() => (gameRunning = true)}
    >
        <h1 class="mx-auto text-4xl font-bold">RPS Battle</h1>
        <TextInput
            label="Battle name (Optional)"
            placeholder="the lord of the ring"
            bind:value={settings.battleName}
        />
        <RangeInput
            step={2}
            min={1}
            max={30}
            bind:value={settings.stepsPerSecond}
            label="Steps per second"
        />
        <div class="flex flex-row gap-4">
            <TextInput
                label="Game width"
                placeholder="640"
                bind:value={settings.width}
                type="number"
                class="flex-1"
            />
            <TextInput
                label="Game height"
                placeholder="480"
                bind:value={settings.height}
                type="number"
                class="flex-1"
            />
        </div>
        <Button submit color={Color.accent} class="justify-center h-10">
            Start!
        </Button>
    </form>
{:else}
    <Game
        width={Number(settings.width)}
        height={Number(settings.height)}
        speed={settings.stepsPerSecond}
        name={settings.battleName}
    />
{/if}
