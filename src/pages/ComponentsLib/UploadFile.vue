<script setup lang="ts">
import { ref, onMounted } from "vue";
const uploader = ref(null)
const files = ref([])

function dropFile(e) {
  e.preventDefault()
  files.value = []
  console.log(e.dataTransfer.files);
  for (let i = 0; i < e.dataTransfer.files.length; i++) {
    const file = e.dataTransfer.files[i]
    files.value.push(e.dataTransfer.files[i].name)
    const s = performance.now()
    splitFile(e.dataTransfer.files[i], 0, 1024 * 1024 * 50).then(
      (data) => { console.log(performance.now() - s); console.log(data); }
    )
  }
}

async function splitFile(file, start, chunkSize) {
  const chunkNum = Math.ceil(file.size / chunkSize)
  const chunks = []
  for (let i = 0; i < chunkNum; i++) {
    const chunk = file.slice(start + i * chunkSize, start + (i + 1) * chunkSize)
    const buffer = new ArrayBuffer(chunk)
    const hash = await crypto.subtle.digest('SHA-256', buffer)
    chunks.push({ chunk, hash })
  }
  return chunks
}
</script>

<template>
  <div flex flex-col items-center>
    <h1>Upload File</h1>
    <div>
      <div>Progress bar</div>
      <div>File name: {{ files }}</div>
    </div>
    <input type="file" name="image" multiple accept="*">
    <div ref="uploader" class="upload-container" @drop="dropFile" @dragover.prevent="">
      ➕把文件拖到此处
    </div>
  </div>
</template>

<style>
.upload-container {
  height: 200px;
  width: 200px;
  padding: 0;
  display: flex;
  justify-content: center;
  align-items: center;
  border: 1px dashed sandybrown;
}
</style>