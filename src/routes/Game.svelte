<script lang="ts">
    import { onMount } from 'svelte/internal'

    export let width: number
    export let height: number
    export let name = ''
    export let speed: number

    let playing = false

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
            let minDistance = Infinity
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
            if (!this.target || !this.canAttack(this.target.type)) {
                this.target = this.findTarget(agents)
            }

            for (const agent of agents) {
                const distance = Math.sqrt(
                    Math.abs(agent.x - this.x) ** 2 +
                        Math.abs(agent.y - this.y) ** 2
                )

                if (distance < 30 && agent.canAttack(this.type)) {
                    this.type = agent.type
                }
            }

            // If there still is no target, move away
            const speed = this.target ? 2 : -1

            if (!this.target) {
                this.target = this.findAvoid(agents)
            }

            if (!this.target) {
                return
            }

            const dx = this.target.x - this.x
            const dy = this.target.y - this.y
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

    export let agentCount = 50

    onMount(() => {
        playing = true

        const agents = [
            new Agent(50, 50, 'paper'),
            new Agent(100, 150, 'rock'),
            new Agent(200, 100, 'scissors'),
        ]

        for (let i = 1; i <= agentCount - 3; i++) {
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
        context.font = '24px sans-serif'

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

        setInterval((id: number) => {
            if (playing) {
                context.clearRect(0, 0, width, height)
                draw()
            } else {
                clearInterval(id)
            }
        }, (1 / speed) * 100)
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
</div>
