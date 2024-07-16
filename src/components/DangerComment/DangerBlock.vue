<script setup lang="ts">
defineProps<{ active: boolean; text: string; reason: string; top?: number }>();

</script>
<template>
  <div class="normal-block" :class="{ 'active-block': active }" :style="{ top: `${top ?? 0}px` }">
    <p style="color: gray; border-left: 1px solid gray; padding-left: 6px">{{ text }}</p>
    <p class="p-2">{{ reason }}</p>
  </div>
</template>
<style scoped>
.normal-block {
  position: absolute;
  right: 0;
  width: 240px;
  border: 1px solid rgba(197, 191, 191, 0.478);
  border-radius: 6px;
  padding: 4px;
  transition: all 0.3s;
}
.active-block {
  border-top: 1px red solid !important;
  right: 20px;
}
</style>
