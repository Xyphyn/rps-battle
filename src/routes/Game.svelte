<script lang="ts">
    import Button from '$lib/ui/Button.svelte'
    import { Color } from '$lib/ui/colors'
    import { onMount } from 'svelte/internal'

    export let width: number
    export let height: number
    export let name = ''
    export let speed: number
    export let agentCount: number
    export let experimentalAI: boolean

    let playing = false

    export let menu = true

    type AgentType = 'rock' | 'paper' | 'scissors'

    class Agent {
        x: number
        y: number
        target: Agent | undefined
        type: AgentType

        constructor(x: number, y: number, type: AgentType) {
            this.x = x
            this.y = y
            this.type = type
        }

        canAttack(type: AgentType) {
            let attackableType: AgentType

            switch (this.type) {
                case 'paper': {
                    attackableType = 'rock'
                    break
                }
                case 'rock': {
                    attackableType = 'scissors'
                    break
                }
                case 'scissors': {
                    attackableType = 'paper'
                    break
                }
            }

            return attackableType == type
        }

        findTarget(agents: Agent[]): Agent | undefined {
            let minDistance = Infinity
            let closestAgent: Agent | undefined

            for (const agent of agents) {
                if (
                    agent == this ||
                    agent.type == this.type ||
                    !this.canAttack(agent.type)
                )
                    continue

                const distance = Math.sqrt(
                    Math.abs(agent.x - this.x) ** 2 +
                        Math.abs(agent.y - this.y) ** 2
                )
                if (distance < minDistance) {
                    minDistance = distance

                    closestAgent = agent
                }
            }

            return closestAgent
        }

        findAvoid(agents: Agent[]): Agent | undefined {
            let minDistance = 100
            let closestAgent: Agent | undefined

            for (const agent of agents) {
                if (
                    agent == this ||
                    agent.type == this.type ||
                    this.canAttack(agent.type)
                )
                    continue

                const distance = Math.sqrt(
                    Math.abs(agent.x - this.x) ** 2 +
                        Math.abs(agent.y - this.y) ** 2
                )
                if (distance < minDistance) {
                    minDistance = distance

                    closestAgent = agent
                }
            }

            return closestAgent
        }

        updatePosition(agents: Agent[]) {
            this.target = this.findTarget(agents)

            for (const agent of agents) {
                const distance = Math.sqrt(
                    Math.abs(agent.x - this.x) ** 2 +
                        Math.abs(agent.y - this.y) ** 2
                )

                if (distance < 20 && agent.canAttack(this.type)) {
                    this.type = agent.type
                }
            }

            const avoid = this.findAvoid(agents)

            const speed = 2

            // Avoid the predators with a higher priority
            if (avoid && experimentalAI) {
                const dx = avoid.x - this.x
                const dy = avoid.y - this.y
                const distance = Math.sqrt(dx * dx + dy * dy)

                // Calculate the angle between the agent and the target point
                const angle = Math.atan2(dy, dx)

                // Calculate the x and y components of the movement vector
                const vx = speed * -1 * Math.cos(angle)
                const vy = speed * -1 * Math.sin(angle)

                // Move the agent towards the target point
                if (this.x + vx < width && this.x + vx > 0) {
                    this.x += vx
                }

                if (this.y + vy < height && this.y + vy > 0) {
                    this.y += vy
                }
            }

            if (this.target) {
                const dx =
                    this.target.x -
                    this.x +
                    (experimentalAI ? Math.random() * 20 : 0)
                const dy =
                    this.target.y -
                    this.y +
                    (experimentalAI ? Math.random() * 20 : 0)
                const distance = Math.sqrt(dx * dx + dy * dy)

                // Calculate the angle between the agent and the target point
                const angle = Math.atan2(dy, dx)

                // Calculate the x and y components of the movement vector
                const vx = speed * Math.cos(angle)
                const vy = speed * Math.sin(angle)

                // Move the agent towards the target point
                if (this.x + vx < width && this.x + vx > 0) {
                    this.x += vx
                }

                if (this.y + vy < height && this.y + vy > 0) {
                    this.y += vy
                }
            }
        }
    }

    let agents: Agent[] = []

    onMount(async () => {
        playing = true
        menu = false

        agents = []

        for (let i = 1; i <= agentCount; i++) {
            const random = i % 3

            agents.push(
                new Agent(
                    Math.random() * (width - 40 + 20),
                    Math.random() * (height - 40 + 20),
                    random == 0
                        ? 'paper'
                        : random == 1
                        ? 'rock'
                        : random == 2
                        ? 'scissors'
                        : 'scissors'
                )
            )
        }

        const canvas = document.getElementById('canvas')

        // @ts-ignore
        const context = canvas.getContext('2d')
        context.font = '20px sans-serif'

        function draw() {
            for (const agent of agents) {
                context.fillText(
                    agent.type == 'paper'
                        ? '📜'
                        : agent.type == 'scissors'
                        ? '✂️'
                        : agent.type == 'rock'
                        ? '🪨'
                        : '🪨',
                    agent.x,
                    agent.y
                )

                agent.updatePosition(agents)
            }
        }

        const interval = setInterval(() => {
            if (playing) {
                context.clearRect(0, 0, width, height)
                draw()
                checkWin()
            } else {
            }
        }, (1 / speed) * 1000)

        function checkWin(): boolean {
            let firstType = agents[0].type

            for (const agent of agents) {
                if (agent.type != firstType) return false
            }

            clearInterval(interval)
            playing = false

            return true
        }
    })
</script>

<div class="flex flex-col gap-8 justify-center items-center">
    <p class="text-3xl font-bold">{name || 'RPS battle'}</p>
    <canvas
        {width}
        {height}
        id="canvas"
        class="rounded-xl border border-dashed border-black/50 dark:border-white/50"
    />
    {#if !playing}
        <p>{agents[0]?.type} wins wooooo</p>
    {/if}
    <div class="flex flex-row">
        <Button onclick={() => (menu = true)} color={Color.accent}>
            Return to menu
        </Button>
    </div>
</div>
