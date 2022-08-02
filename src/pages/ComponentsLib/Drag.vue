<script setup lang="ts">
let startMoving: boolean = $ref(false)
let lastTop: number = $ref(0) // result top value
let elementY: number = $ref(0)
let elementHeight: number = $ref(0)
let parentY: number = $ref(0)
let parentHeight: number = $ref(0)
let activeId: number = $ref(-1)
let target: HTMLElement = $ref()
let duplication: HTMLElement = $ref()

const targetStyle: string = $computed(() => `z-index:1001; opacity: 50%; top: ${lastTop}px; position: ${startMoving ? 'absolute' : 'relative'}`)

interface iData {
  id: number
  text: string
}
let dataList: iData[] = $ref([])
dataList = [
  {
    id: 0,
    text: '111',
  },
  {
    id: 1,
    text: '222',
  },
  {
    id: 2,
    text: '333',
  }
]

const mousedown = (id: number, e: MouseEvent) => {
  startMoving = true
  activeId = id
  target = e.target as HTMLElement
  elementY = e.pageY - target.getBoundingClientRect().top
  elementHeight = target.getBoundingClientRect().bottom - target.getBoundingClientRect().top
  parentY = target.parentElement?.getBoundingClientRect().top!
  parentHeight = target.parentElement?.getBoundingClientRect().bottom! - target.parentElement?.getBoundingClientRect().top!
  lastTop = target.getBoundingClientRect().top - parentY

  // create a duplication as a placeholder
  duplication = document.createElement('li')
  duplication.textContent = target.textContent
  duplication.style.borderStyle = 'dotted'
  duplication.style.borderWidth = '1px'
  duplication.style.marginBottom = '2rem'
  duplication.id = 'duplication'
  target.parentNode?.insertBefore(duplication, e.target as Node)

  document.addEventListener('mousemove', (e) => onMouseMove(e))
}

const onMouseMove = (e: MouseEvent) => {
  if (startMoving) {
    const temp = e.pageY - parentY - elementY
    if (temp < 0) lastTop = 0
    else if (temp > parentHeight - elementHeight) lastTop = parentHeight - elementHeight
    else lastTop = temp
  }
}

document.addEventListener('mouseup', () => {
  startMoving = false
  activeId = -1
  document.getElementById('duplication')?.remove()
  document.removeEventListener('mousemove', onMouseMove)
})
</script>

<template>
  <div w-full p-10 flex flex-row justify-center>
    <ul id="ul" w-40 h-50 relative>
      <li v-for="item in dataList" :key="item.id" w-40
        :style="[activeId === item.id ? `${targetStyle}` : '', startMoving ? `opacity: 0.5` : '']"
        :class="[activeId === item.id ? '' : 'droppable']" @mousedown="mousedown(item.id, $event)">
        {{
            item.text
        }}
      </li>
    </ul>
    <pre text-3 text-start w-40>{{ JSON.stringify(dataList, null, 2) }}</pre>
  </div>
  <div>
    lastTop: {{ lastTop }}px
    parentHeight {{parentHeight}}
  </div>
</template>

<style scoped>
ul {
  background-color: aliceblue;
}

li {
  background-color: rgb(191, 254, 233);
  margin-bottom: 2rem;
  user-select: none;
}
</style>
