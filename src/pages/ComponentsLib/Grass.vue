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
    theta: PI * getRandomBetween(0.25, 0.75)
  }
  function draw(start: PointA, end: PointB, random: number = 0) {
    ctx.beginPath()
    ctx.moveTo(start.x, start.y)
    ctx.lineTo(end.x, end.y)
    ctx.strokeStyle = '#9ca3af'
    ctx.stroke()
    if (start.x >= 500 || start.x <= 0 || start.y <= 0 || start.y >= 500) return
    let randomAlpha = getRandomBetween(0.2, 0.8) * PI
    let randomBeta = getRandomBetween(0.2, 0.8) * PI
    while (end.theta === randomAlpha) {
      randomAlpha = getRandomBetween(0.1, 0.5) * PI
    }
    while (end.theta === randomBeta) {
      randomBeta = getRandomBetween(0.1, 0.5) * PI
    }

    let length
    if (getRandomBetween(0, 1) > random) {
      length = getRandomBetween(10, 50)
      draw(end, { x: end.x + length * Math.cos(randomAlpha), y: end.y - length * Math.sin(randomAlpha), theta: randomAlpha }, random + 0.07)
    }
    if (getRandomBetween(0, 1) > random) {
      length = getRandomBetween(10, 50)
      draw(end, { x: end.x + length * Math.cos(randomBeta), y: end.y - length * Math.sin(randomBeta), theta: randomBeta }, random + 0.07)
    }
  }
  draw(start, end)
})
</script>

<template>
  <div w-full flex flex-row justify-center p-6>
    <canvas id="canvas" width="400" height="400" border-1 border-gray-4></canvas>
  </div>
  <p><span @click="refresh" cursor-pointer>Refresh</span> and see the result.</p>
</template>