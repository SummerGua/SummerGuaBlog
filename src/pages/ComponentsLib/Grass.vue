<script setup lang="ts">
const refresh = () => {
  location.reload()
}

onMounted(() => {
  const PI = Math.PI
  const canvas = document.getElementById('canvas')! as HTMLCanvasElement
  const ctx = canvas.getContext('2d')!

  function getRandomBetween(min: number, max: number): number {
    return Math.random() * (max - min) + min;
  }

  interface PointA {
    x: number
    y: number
  }
  interface PointB {
    x: number
    y: number
    theta: number
  }

  let start: PointA = {
    x: canvas.width / 2,
    y: canvas.height
  }
  let end: PointB = {
    x: canvas.width / 2,
    y: canvas.height - 20,
    theta: PI * 0.5
  }

  const tasks: Function[] = []

  function draw(start: PointA, end: PointB, random: number = 0) {
    ctx.beginPath()
    ctx.moveTo(start.x, start.y)
    ctx.lineTo(end.x, end.y)
    ctx.strokeStyle = '#9ca3af'
    ctx.stroke()
    if (start.x >= 500 || start.x <= 0 || start.y <= 0 || start.y >= 500) return
    let randomAlpha = end.theta + getRandomBetween(0.1, 0.2) * PI
    let randomBeta = end.theta - getRandomBetween(0.1, 0.2) * PI

    let endLeft: boolean = true
    let endRight: boolean = true

    if (getRandomBetween(0, 1) > random) {
      endLeft = false
      tasks.push(() => {
        const length = getRandomBetween(20, 30)
        draw(end, { x: end.x + length * Math.cos(randomAlpha), y: end.y - length * Math.sin(randomAlpha), theta: randomAlpha }, random + 0.06)
      })
    }
    if (getRandomBetween(0, 1) > random) {
      endRight = false
      tasks.push(() => {
        const length = getRandomBetween(20, 30)
        draw(end, { x: end.x + length * Math.cos(randomBeta), y: end.y - length * Math.sin(randomBeta), theta: randomBeta }, random + 0.06)
      })
    }
    if (endLeft && endRight) {
      drawCircle(end.x, end.y, 5)
    }
  }

  function drawCircle(x: number, y: number, r: number) {
    ctx.beginPath();
    ctx.arc(x, y, r, 0, 2 * Math.PI);
    ctx.strokeStyle = '#b56271'
    ctx.stroke();
  }

  draw(start, end)

  function oneStep() {
    const thisTasks = [...tasks]
    tasks.length = 0
    thisTasks.forEach(fn => fn())
  }

  let frameCount = 0
  function startDraw() {
    requestAnimationFrame(() => {
      frameCount++
      if (frameCount % 2 === 0) {
        oneStep()
      }
      startDraw()
    })
  }
  startDraw()
})
</script>

<template>
  <div w-full flex flex-row justify-center p-6>
    <canvas id="canvas" width="400" height="400" border-1 border-gray-4></canvas>
  </div>
  <p><span @click="refresh" cursor-pointer>Refresh</span> and see the result.</p>
</template>