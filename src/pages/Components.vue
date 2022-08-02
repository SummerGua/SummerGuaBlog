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
      <li><a href="https://timepointer.netlify.app/" target="_blank" underline>2. Time Pointer [open in Netlify↗]</a></li>
      <li><a href="https://nnuclass.netlify.app" target="_blank" underline>3. Class Schedule [open in Netlify↗]</a></li>
    </ol>
  </div>
</template>

<style scoped>
li {
  cursor: pointer;
  margin: 1rem;
}
</style>
