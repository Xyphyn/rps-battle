<script lang="ts">
    export let min = 10
    export let max = 0
    export let value = 0
    export let step = 1
    export let label = ''

    let clazz = ''
    export { clazz as class }
</script>

<label class="flex flex-col items-center {clazz} group">
    {#if label != ''}
        <span
            class="self-start mb-1 w-max text-sm font-bold text-left cursor-pointer"
        >
            {label}
        </span>
    {/if}
    <input
        bind:value
        type="range"
        {min}
        {max}
        {step}
        class="slider mt-2 {clazz}"
    />
    <div class="flex flex-row justify-between m-2 w-full text-sm opacity-50">
        <span class="">{min}</span>
        <span class="">{max}</span>
    </div>
</label>

<style lang="postcss">
    /* The slider itself */
    .slider {
        -webkit-appearance: none; /* Override default CSS styles */
        appearance: none;
        width: 100%;
        outline: none;
        opacity: 0.7;
        @apply bg-slate-200 dark:bg-zinc-700 h-2 rounded-full transition-opacity;
    }

    /* Mouse-over effects */
    .slider:hover {
        opacity: 1; /* Fully shown on mouse-over */
    }

    /* The slider handle (use -webkit- (Chrome, Opera, Safari, Edge) and -moz- (Firefox) to override default look) */
    .slider::-webkit-slider-thumb {
        -webkit-appearance: none;
        appearance: none;
        @apply bg-blue-400 w-4 h-4 rounded-full;
    }

    .slider::-moz-range-thumb {
        @apply bg-blue-400 w-4 h-4 rounded-full;
    }
</style>
