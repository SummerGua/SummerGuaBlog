<script setup>
import { computed, onMounted, reactive, ref } from 'vue';
const dataSource = ref(new Array(1000).fill(0).map((_, index) => index))
const itemHeight = 40;
const containerHeight = 200;
const itemSize = containerHeight / itemHeight
const list = ref(null)
const container2 = ref(null)

const more = 6

const range = reactive({
  startIndex: 0,
  endIndex: itemSize * 2
})
const renderSource = computed(() => (dataSource.value.slice(range.startIndex, range.endIndex)))

onMounted(() => {
  container2.value.addEventListener('scroll', rafThrottle(() => {
    const newStartIndex = Math.floor(container2.value.scrollTop / itemHeight)
    if (newStartIndex !== range.startIndex) {
      range.startIndex = newStartIndex
      range.endIndex = range.startIndex + itemSize + more
    }
  }))
})

function rafThrottle(fn) {
  let lock = false;
  return function (...args) {
    window.requestAnimationFrame(() => {
      if (lock) return;
      lock = true;
      fn.apply(this, args);
      lock = false;
    });
  };
}

</script>
<template>
  <div class="container1">
    <div
      ref="container2"
      class="container2"
    >
      <div
        ref="list"
        class="list"
        :style="{ height: (dataSource.length - range.startIndex) * itemHeight + 'px', transform: `translate3d(0, ${range.startIndex * itemHeight}px,0)` }"
      >
        <div
          v-for="(item, index) in renderSource"
          :key="index"
          class="list-item"
        >
          {{ item }}
        </div>
      </div>
    </div>
  </div>
</template>
<style scoped>
.container1 {
  height: 200px;
  width: 200px;
  padding: 0;
  border: 1px solid sandybrown;
  overflow: hidden;
}

.container2 {
  width: 100%;
  height: 100%;
  overflow-y: auto;
}

.list {
  width: 100%;
}

.list-item {
  width: 100%;
  height: 40px;
  border-bottom: 1px solid steelblue;
  background-color: #ccc;
  display: flex;
  justify-content: center;
  align-items: center;
}
</style>