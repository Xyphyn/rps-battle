<script>
    import Stopwatch from '$lib/Stopwatch.svelte'
    import RangeInput from '$lib/ui/input/RangeInput.svelte'
    import SwitchInput from '$lib/ui/input/SwitchInput.svelte'
    import TextInput from '$lib/ui/input/TextInput.svelte'
    import Button from '../lib/ui/Button.svelte'
    import { Color } from '../lib/ui/colors'
    import Game from './Game.svelte'

    let settings = {
        battleName: '',
        stepsPerSecond: 20,
        width: '640',
        height: '480',
        agents: 100,
        experimentalAI: true,
    }

    let menu = true

    function start() {
        menu = false
    }
</script>

{#if menu}
    <form
        class="flex flex-col gap-8 p-8 mx-auto max-w-xl bg-white rounded-md shadow-md dark:bg-zinc-900"
        on:submit|preventDefault={start}
    >
        <h1 class="mx-auto text-4xl font-bold">RPS Battle</h1>
        <TextInput
            label="Battle name (Optional)"
            placeholder="the lord of the ring"
            bind:value={settings.battleName}
        />
        <RangeInput
            step={2}
            min={5}
            max={60}
            bind:value={settings.stepsPerSecond}
            label="Steps per second"
        />
        <RangeInput
            step={1}
            min={5}
            max={300}
            bind:value={settings.agents}
            label="Agent count"
        />
        <div class="flex flex-col gap-4 md:flex-row">
            <TextInput
                label="Game width"
                placeholder="640"
                bind:value={settings.width}
                type="number"
                class="md:flex-1"
            />
            <TextInput
                label="Game height"
                placeholder="480"
                bind:value={settings.height}
                type="number"
                class="md:flex-1"
            />
            <Button
                class="h-11 md:flex-1 md:self-end"
                color={Color.ghost}
                onclick={() => {
                    settings.width = `${document.body.clientWidth - 50}`
                    settings.height = `${document.body.clientHeight - 200}`
                }}
            >
                Screen size
            </Button>
        </div>
        <div class="flex flex-row gap-2 items-center">
            <SwitchInput bind:enabled={settings.experimentalAI} />
            <div class="flex flex-col">
                <span class="block text-sm font-bold">Experimental AI</span>
                <span class="text-xs opacity-80">
                    Agents are more cautious when attacking "prey", avoiding
                    "predators" along the way.
                </span>
            </div>
        </div>
        <Button submit color={Color.accent} class="justify-center h-10">
            Start!
        </Button>
        <hr class="w-[80%] mx-auto opacity-30" />
        <p class="mx-auto text-sm opacity-40">
            Leave feedback on the <a
                href="https://github.com/Xyphyn/rps-battle"
                class="text-white underline"
            >
                GitHub
            </a>
        </p>
    </form>
{:else}
    <Game
        width={Number(settings.width)}
        height={Number(settings.height)}
        speed={settings.stepsPerSecond}
        name={settings.battleName}
        agentCount={settings.agents}
        experimentalAI={settings.experimentalAI}
        bind:menu
    />
{/if}
