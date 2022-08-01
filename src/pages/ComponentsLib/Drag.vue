<script setup lang="ts">
let startMoving: boolean = $ref(false)
// let realElementY: number = $ref(0) // record mousedown innerY
let lastTop: number = $ref(0) // result top value
let elementY: number = $ref(0)
let activeId: number = $ref(-1)

let targetStyle: string = $computed(() => `opacity: 50%; top: ${lastTop}px; position: ${startMoving ? 'absolute' : 'relative'}`)

interface iData {
  id: number
  text: string
}
let dataList: iData[] = $ref([])
dataList = [
  {
    id: 1,
    text: '111'
  },
  {
    id: 2,
    text: '222'
  },
  {
    id: 3,
    text: '333'
  }
]

const mousedown = (id: number, e: MouseEvent) => {
  startMoving = true
  const target = e.target as HTMLElement
  elementY = e.pageY - target.getBoundingClientRect().top
  lastTop = target.getBoundingClientRect().top
  activeId = id

  // create a duplication as a placeholder
  let duplication = document.createElement('li')
  duplication.textContent = target.textContent
  duplication.style.borderStyle = 'dotted'
  duplication.style.borderWidth = '1px'
  duplication.style.marginBottom = '2rem'
  duplication.id = 'duplication'
  target.parentNode?.insertBefore(duplication, e.target as Node);

  document.addEventListener('mousemove', (e) => {
    if (startMoving && lastTop >= 0) {
      lastTop = e.pageY - elementY
      console.log(document.elementFromPoint(e.clientX, e.clientY)?.closest('.droppable'))
      // check position and change data
    } else {
      lastTop = 0
    }
  })

  document.addEventListener('mouseup', () => {
    startMoving = false
    activeId = -1
    document.getElementById('duplication')?.remove()
  })
}

</script>

<template>
  <div w-full>
    <ul id="ul" w-40 h-50 relative>
      <li v-for="item in dataList" :key="item.id" @mousedown="mousedown(item.id, $event)" w-40
        :style="[activeId === item.id ? `${targetStyle}` : '', startMoving ? `opacity: 0.5` : '']"
        :class="[activeId === item.id ? '' : 'droppable']">{{
            item.text
        }}</li>
    </ul>
  </div>
  <pre style="margin: 0 auto" text-2 text-start w-40>
    dataList: {{ JSON.stringify(dataList, null, 2) }}
  </pre>
</template>

<style scoped>
ul {
  margin: 0 auto;
  background-color: aliceblue;
}

li {
  background-color: rgb(191, 254, 233);
  margin-bottom: 2rem;
  user-select: none;
}
</style>