<script setup lang="ts">
const router = useRouter()
const route = useRoute()
const back = () => {
  router.back()
}
</script>

<template>
  <main font-sans p="x-4" text="center gray-700 dark:gray-200">
    <router-view />
    <div v-if="route.path !== '/'" m-4>
      <button bg-gray p-1 w-20 border-rounded @click="back()">
        back
      </button>
    </div>
    <Footer />
  </main>
</template>
