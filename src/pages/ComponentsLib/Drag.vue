<script setup lang="ts">
let startMoving: boolean = $ref(false)
let lastTop: number = $ref(0) // result top value
let initTop: number = $ref(0)
let elementY: number = $ref(0)
let parentY: number = $ref(0)
let activeId: number = $ref(-1)
let target: HTMLElement = $ref()
let duplication: HTMLElement = $ref()
let flag: boolean = $ref(true)

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
  },
]

const mousedown = (id: number, e: MouseEvent) => {
  startMoving = true
  activeId = id
  target = e.target as HTMLElement
  elementY = e.pageY - target.getBoundingClientRect().top
  parentY = target.parentElement?.getBoundingClientRect().top!
  lastTop = target.getBoundingClientRect().top - parentY
  initTop = lastTop

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
  if (startMoving && flag) {
    lastTop = (e.pageY - elementY - parentY) >= 0 ? (e.pageY - elementY - parentY) : 0
    // check position and change data
    const preSibling = target.previousElementSibling?.previousElementSibling
    const nextSibling = target.nextElementSibling?.nextElementSibling
    if (preSibling) {
      if (lastTop <= (preSibling.getBoundingClientRect().top + preSibling.scrollHeight)) {
        flag = false
        // swap
        console.log('swap?')
        flag = true
      }
    }
    else if (nextSibling) {
      if ((lastTop + elementY + parentY) >= nextSibling.getBoundingClientRect().top) {
        flag = false
        // swap
        console.log('swap?')
        flag = true
      }
    }
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
    <pre text-2 text-start w-40>
    dataList: {{ JSON.stringify(dataList, null, 2) }}
  </pre>
  </div>
  <div>
    activeId: {{ activeId }}<br />
    lastTop: {{ lastTop + elementY + parentY }}px
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
