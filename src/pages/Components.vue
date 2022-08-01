<script setup lang="ts">
const router = useRouter()
const go = (component: string) => {
  router.push(`/ComponentsLib/${component}`)
}
</script>

<template>
  <div p-6 flex flex-col>
    <ol w-80 text-left self-center>
      <li @click="go('Drag')">
        1. Drag
      </li>
      <li><a>2. Time Pointer</a></li>
      <li><a>3. Class Schedule</a></li>
    </ol>
  </div>
</template>

<style scoped>
li {
  cursor: pointer;
}
</style>
