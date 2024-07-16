<script setup lang="ts">
const router = useRouter()
const go = (component: string) => {
  router.push(`/ComponentsLib/${component}`)
}
</script>

<template>
  <div p-6 flex flex-col>
    <ol w-80 text-left self-center>
      <li>
        1. Time Pointer <a href="https://timepointer.netlify.app/" target="_blank"><span cursor-pointer underline
            text-gray-4>[open in Netlify↗]</span></a>
      </li>
      <li>
        2. Class Schedule <a href="https://nnuclass.netlify.app" target="_blank"><span cursor-pointer underline
            text-gray-4>[open in Netlify↗]</span></a>
      </li>
      <li @click="go('Drag')" cursor-pointer>3. Drag</li>
      <li @click="go('Grass')" cursor-pointer>4. Grass</li>
      <li @click="go('VirtualList')" cursor-pointer>5. VirtualList</li>
      <li @click="go('HighlightComment')" cursor-pointer>6. HighlightComment</li>
      <li @click="go('UploadFile')" cursor-pointer>7. UploadFile</li>
    </ol>
  </div>
</template>

<style scoped>
li {
  margin: 1rem;
}
</style>
