<script setup lang="ts">
const router = useRouter()
const go = (page: string) => {
    router.push(`${page}`)
}
</script>

<template>
  <div>
    <p m-b-3>
      <a text-size-6>
        SummerGua
      </a>
    </p>
    <ul text-size-4>
      <li cursor-pointer>Articles</li>
      <li cursor-pointer @click="go('Components')">Components</li>
      <li cursor-pointer @click="go('About')">About</li>
    </ul>
  </div>
</template>

<style scoped>
li {
  margin: 1rem;
}
</style>