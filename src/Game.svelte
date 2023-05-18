<script lang="ts">
    import { Canvas, Layer, t } from 'svelte-canvas'
    import { onMount } from 'svelte/internal'

    let width = document.body.clientWidth
    let height = document.body.clientHeight

    type AgentType = 'rock' | 'paper' | 'scissors'

    class Agent {
        x: number
        y: number
        target: Agent
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

        findTarget(agents: Agent[]): Agent {
            let minDistance = Infinity
            let closestAgent: Agent

            for (const agent of agents) {
                if (agent == this || agent.type == this.type) continue

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

            // If there still is no target, move randomly
            if (!this.target) {
                return
            }

            const dx = this.target.x - this.x
            const dy = this.target.y - this.y
            const distance = Math.sqrt(dx * dx + dy * dy)

            // Calculate the angle between the agent and the target point
            const angle = Math.atan2(dy, dx)

            // Calculate the x and y components of the movement vector
            const speed = 2
            const vx = speed * Math.cos(angle)
            const vy = speed * Math.sin(angle)

            // Move the agent towards the target point
            this.x += vx
            this.y += vy
        }
    }

    export let agentCount = 50

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

    onMount(() => {
        const canvas = document.getElementById('canvas')

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

        setInterval(() => {
            context.clearRect(0, 0, width, height)
            draw()
        }, 50)
    })
</script>

<canvas {width} {height} id="canvas" />
