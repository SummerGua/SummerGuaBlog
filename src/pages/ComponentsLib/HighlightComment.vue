<script setup lang="ts">
import { ref, reactive, onMounted } from 'vue';
import DangerBlock from '~/components/DangerComment/DangerBlock.vue';
import DangerSpan from '~/components/DangerComment/DangerSpan.vue';

let activeDangerId = ref('');
const dangerInfoFromAPI = [
  {
    id: 'danger0',
    text: '000',
    reason: '引流0'
  },
  {
    id: 'danger1',
    text: 'danger1',
    reason: '引流引流引流引流引流引流引流引流引流引流引流引流引流引流引流引流引流引流'
  },
  {
    id: 'danger2',
    text: '222',
    reason: '涉政涉政涉政涉政涉政222'
  },
  {
    id: 'danger3',
    text: '333',
    reason: '涉政涉政涉政涉政涉政3333'
  }
];
let tops = reactive({});
dangerInfoFromAPI.forEach((item) => {
  tops[item.id] = { shouldBe: -999, height: -999, actualTop: -999 };
});

function setBlockHeight(param: { dangerId: string; top: number }) {
  tops[param.dangerId].shouldBe = param.top;
  tops[param.dangerId].actualTop = param.top;
}
const gap = 10;
function initCompute() {
  const dangerBlocks = document.querySelectorAll('.normal-block');
  const newTops = { ...tops };
  if (dangerBlocks.length > 1) {
    const firstHeight = dangerBlocks[0].getBoundingClientRect().height;
    newTops[dangerInfoFromAPI[0].id].height = firstHeight;
    for (let i = 1; i < dangerBlocks.length; i++) {
      const id = dangerInfoFromAPI[i].id;
      newTops[id].height = dangerBlocks[i].getBoundingClientRect().height;
      const lastOne = dangerBlocks[i - 1];
      const lastOneBottom =
        lastOne.getBoundingClientRect().height +
        newTops[dangerInfoFromAPI[i - 1].id].actualTop +
        gap;
      if (newTops[id].actualTop < lastOneBottom) {
        newTops[id].actualTop = lastOneBottom;
      }
    }
    tops = newTops;
  }
}

onMounted(initCompute);

function enterDanger(id: string) {
  activeDangerId.value = id;

  const currentIndex = dangerInfoFromAPI.findIndex((item) => item.id === id);
  if (dangerInfoFromAPI.length < 2) return;

  let topsCopy = { ...tops }; // 最后再调整位置，防止看起来挨个突变或过多reflow
  if (topsCopy[id].actualTop !== topsCopy[id].shouldBe) {
    // 先移动自己
    const moveUpDistance = topsCopy[id].actualTop - topsCopy[id].shouldBe;
    topsCopy[id].actualTop = topsCopy[id].shouldBe;

    if (moveUpDistance > 0) {
      // 上移，导致上面重叠的元素顺带往上移，
      // 下面不在原位的元素恢复原位，恢复不了就也移动moveUpDistance
      for (let i = currentIndex - 1; i >= 0; i--) {
        const upperId = dangerInfoFromAPI[i].id;
        const formerId = dangerInfoFromAPI[i + 1].id;
        if (
          topsCopy[upperId].actualTop + topsCopy[upperId].height + gap >
          topsCopy[formerId].actualTop
        ) {
          topsCopy[upperId].actualTop = topsCopy[upperId].actualTop - moveUpDistance;
        }
      }
      for (let i = currentIndex + 1; i < dangerInfoFromAPI.length; i++) {
        const lowerId = dangerInfoFromAPI[i].id;
        const formerId = dangerInfoFromAPI[i - 1].id;
        if (topsCopy[lowerId].actualTop !== topsCopy[lowerId].shouldBe) {
          if (
            topsCopy[lowerId].shouldBe <
            topsCopy[formerId].actualTop + topsCopy[formerId].height + gap
          ) {
            topsCopy[lowerId].actualTop =
              topsCopy[formerId].actualTop + topsCopy[formerId].height + gap;
          } else {
            topsCopy[lowerId].actualTop = topsCopy[lowerId].shouldBe;
          }
        }
      }
    } else {
      // 下移，下面重叠的元素也跟着下移
      for (let i = currentIndex + 1; i < dangerInfoFromAPI.length; i++) {
        const formerId = dangerInfoFromAPI[i - 1].id;
        const lowerId = dangerInfoFromAPI[i].id;
        if (
          topsCopy[lowerId].actualTop <
          topsCopy[formerId].actualTop + topsCopy[formerId].height + gap
        ) {
          topsCopy[lowerId].actualTop =
            topsCopy[formerId].actualTop + topsCopy[formerId].height + gap;
        }
      }
      for (let i = currentIndex - 1; i >= 0; i--) {
        // 上面不在原位的元素恢复原位，恢复不了就也移动moveUpDistance
        const formerId = dangerInfoFromAPI[i + 1].id;
        const upperId = dangerInfoFromAPI[i].id;
        if (topsCopy[upperId].actualTop !== topsCopy[upperId].shouldBe) {
          // 不在原位
          if (
            topsCopy[upperId].shouldBe >
            topsCopy[formerId].actualTop - topsCopy[upperId].height - gap
          ) {
            topsCopy[upperId].actualTop = topsCopy[upperId].actualTop - moveUpDistance;
          } else {
            topsCopy[upperId].actualTop = topsCopy[upperId].shouldBe;
          }
        }
      }
    }
  }
  tops = topsCopy;
}
function leaveDanger() {
  activeDangerId.value = '';
}

function enterBlock(id: string) {
  activeDangerId.value = id;
}
function leaveBlock() {
  activeDangerId.value = '';
}

let currentTip;

window.addEventListener('mouseup', () => {
  const selection = window.getSelection();
  if (selection?.type !== 'Range') return;
  const rect = selection.getRangeAt(0).getBoundingClientRect();
  const tip = document.createElement('div');
  tip.textContent = 'hello';
  tip.style.position = 'absolute';
  tip.style.left = `${rect.right}px`;
  tip.style.top = `-999px`;
  tip.style.backgroundColor = 'rgb(56,117,246)';
  tip.style.color = 'white';
  tip.style.padding = '4px';
  tip.style.borderRadius = '6px';
  document.body.appendChild(tip);
  tip.style.top = `${rect.top - tip.getBoundingClientRect().height}px`;
  currentTip = tip;
});

window.addEventListener('mousedown', (e) => {
  if (currentTip) {
    currentTip.remove();
    currentTip = undefined;
  }
});
</script>

<template>
  <div flex flex-row justify-center p-4>
    <div class="container">
      <p class="raw-text">
        <DangerSpan :class="[activeDangerId === 'danger0' && 'active-raw']" text="danger0" dangerId="danger0"
          @mouseenter="enterDanger('danger0')" @mouseleave="leaveDanger" @setHeight="setBlockHeight"></DangerSpan>
        基于提效项目，希望针对生态笔记审核中的笔记文本，通过模型能力进行风险提示。
        <DangerSpan :class="[activeDangerId === 'danger1' && 'active-raw']" text="danger1" dangerId="danger1"
          @mouseenter="enterDanger('danger1')" @mouseleave="leaveDanger" @setHeight="setBlockHeight"></DangerSpan>
        ，希望针对生态笔记审核中的笔记文本，通过模型能力进行风险提示。
        <DangerSpan :class="[activeDangerId === 'danger2' && 'active-raw']" text="danger2" dangerId="danger2"
          @mouseenter="enterDanger('danger2')" @mouseleave="leaveDanger" @setHeight="setBlockHeight"></DangerSpan>
        ，希望针对生态笔记审核中的笔记文本，通过模型能力进行风险提示。
        基于提效项目，希望针对生态笔记审核中的笔记文本，通过模型能力进行风险提示。基于提效项目，希望针对生态笔记审核中的笔记文本，通过模型能力进行风险提示。
        基于提效项目，希望针对生态笔记审核中的笔记文本，通过模型能力进行风险提示。基于提效项目，希望针对生态笔记审核中的笔记文本，通过模型能力进行风险提示。
        基于提效项目，希望针对生态笔记审核中的笔记文本，通过模型能力进行风险提示。基于提效项目，希望针对生态笔记审核中的笔记文本，通过模型能力进行风险提示。
        基于提效项目，希望针对生态笔记审核中的笔记文本，通过模型能力进行风险提示。基于提效项目，希望针对生态笔记审核中的笔记文本，通过模型能力进行风险提示。
        <DangerSpan :class="[activeDangerId === 'danger3' && 'active-raw']" @mouseenter="enterDanger('danger3')"
          @mouseleave="leaveDanger" dangerId="danger3" text="danger3" @setHeight="setBlockHeight">
        </DangerSpan>，希望针对生态笔记审核中的笔记文本，通过模型能力进行风险提示。基于提效项目，希望针对生态笔记审核中的笔记文本，通过模型能力进行风险提示。
        基于提效项目，希望针对生态笔记审核中的笔记文本，通过模型能力进行风险提示。
      </p>
      <!-- 右边详情区域 -->
      <div>
        <DangerBlock @mouseenter="enterBlock(item.id)" @mouseleave="leaveBlock()"
          v-for="(item, index) in dangerInfoFromAPI" :key="item.id" :active="item.id === activeDangerId"
          :text="item.text" :reason="item.reason" :top="tops[item.id].actualTop"></DangerBlock>
      </div>
    </div>
  </div>
</template>
<style scoped>
.container {
  position: relative;
  overflow: hidden;
  width: 900px;
  border: 1px solid #ccc;
  padding: 10px;
  display: flex;
  justify-content: space-between;
}

.raw-text {
  width: 600px;
  border-right: 1px solid sandybrown;
}

.active-raw {
  background-color: rgba(255, 209, 21, 0.45);
}
</style>
