<script setup lang="ts">
import { onMounted, ref } from 'vue';

const props = defineProps<{
  dangerId: string;
  text: string;
}>();
const emits = defineEmits(['setHeight']);

const danger = ref();

onMounted(() => {
  emits('setHeight', { dangerId: props.dangerId, top: danger.value.offsetTop });
});
</script>

<template>
  <span ref="danger" class="danger-raw">{{ text }}</span>
</template>
<style scoped>
.danger-raw {
  text-decoration: underline 2px red;
  text-underline-offset: 4px;
  &:hover {
    cursor: pointer;
  }
}
</style>
