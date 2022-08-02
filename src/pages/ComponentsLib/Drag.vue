<script setup lang="ts">
let startMoving: boolean = $ref(false)
let elementY: number = $ref(0)
let elementX: number = $ref(0)
let activeId: number = $ref(-1)
let target: HTMLElement = $ref()
let duplication: HTMLElement = $ref()
let elementListRect: DOMRect[] = $ref([])
let elementList: Array<HTMLElement | Element> = $ref([])
let parent: HTMLElement = $ref()

const duplicationStyle: string = $computed(
  () =>
    `z-index:1001; background-color: rgb(191, 254, 233);
    user-select: none; opacity: 50%;
    position: absolute; width: 10rem; text-align: center;`
)

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
  {
    id: 3,
    text: '444',
  }
]

const getElementList = () => {
  parent = document.getElementById('ul')!
  elementList.length = 0
  elementListRect.length = 0
  for (let i = 0; i < parent.children.length; i++) {
    elementListRect.push(parent.children[i].getBoundingClientRect())
    elementList.push(parent.children[i])
  }
}

onMounted(() => {
  getElementList()
})

const makeDuplication = (): HTMLElement => {
  duplication = document.createElement('div')
  duplication.textContent = target.textContent
  duplication.style.cssText = duplicationStyle
  return duplication
}

const setDupPosition = (ele: HTMLElement, x: number, y: number) => {
  ele.style.left = `${x}px`
  ele.style.top = `${y}px`
}

const isSameElement = (ele: HTMLElement, pos: DOMRect): boolean => {
  const rect = ele.getBoundingClientRect()
  return rect.top === pos.y && rect.left === pos.x
}

const swap = (arr: Array<any>, indexA: number, indexB: number) => {
  const temp = arr[indexA]
  arr[indexA] = arr[indexB]
  arr[indexB] = temp
}

const mousedown = (id: number, e: MouseEvent) => {
  getElementList()
  startMoving = true
  activeId = id
  target = e.target as HTMLElement
  elementY = e.pageY - target.getBoundingClientRect().top
  elementX = e.pageX - target.getBoundingClientRect().left

  duplication = makeDuplication()
  setDupPosition(duplication, e.clientX - elementX, e.clientY - elementY)

  document.body.appendChild(duplication)
  document.addEventListener('mousemove', (e) => onMouseMove(e))
}

const onMouseMove = (e: MouseEvent) => {
  if (startMoving) {
    setDupPosition(duplication, e.clientX - elementX, e.clientY - elementY)
    for (const item of elementListRect) {
      if (!isSameElement(target, item) &&
        e.clientY > item.top && e.clientY < item.bottom &&
        e.clientX > item.left && e.clientX < item.right
      ) {
        let underIndex = elementListRect.indexOf(item)
        let onIndex = elementList.indexOf(target)
        if (onIndex > underIndex) {
          // from bottom to top
          parent.insertBefore(target, elementList[underIndex])
          getElementList()
          if (underIndex - onIndex < -1) {
            while (underIndex !== onIndex) {
              swap(dataList, onIndex, onIndex - 1)
              onIndex--
            }
          } else {
            swap(dataList, onIndex, underIndex)
          }
        }
        else {
          // from top to bottom
          parent.insertBefore(target, elementList[underIndex].nextSibling)
          getElementList()
          if (underIndex - onIndex > 1) {
            while (underIndex !== onIndex) {
              swap(dataList, onIndex, onIndex + 1)
              onIndex++
            }
          } else {
            swap(dataList, onIndex, underIndex)
          }
        }
      }
    }
  }
}

document.addEventListener('mouseup', (e) => {
  // change dataList to update view
  getElementList()
  startMoving = false
  activeId = -1
  duplication.remove()
  document.removeEventListener('mousemove', onMouseMove)
})
</script>

<template>
  <div w-full p-10 flex flex-row justify-center>
    <ul id="ul" w-40 relative>
      <li h-8 lh-8 v-for="item in dataList" :key="item.id" w-40 :class="[activeId === item.id ? 'selected' : '']"
        @mousedown="mousedown(item.id, $event)">
        {{
            item.text
        }}
      </li>
    </ul>
    <pre text-3 text-start w-40>{{ JSON.stringify(dataList, null, 2) }}</pre>
  </div>
</template>

<style scoped>
ul {
  background-color: aliceblue;
}

li {
  background-color: rgb(191, 254, 233);
  margin-bottom: 0.5rem;
  user-select: none;
}

.selected {
  opacity: 50%;
  border: 1px dotted gray;
}
</style>
