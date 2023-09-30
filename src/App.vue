<script setup lang="ts">
import { loadSandpackClient, type ClientOptions, type SandboxSetup, type SandpackClient } from "@codesandbox/sandpack-client"
import { onMounted, ref } from 'vue';

const code = ref(`
  let counter = 0
  setInterval(() => {
    document.getElementById("app").textContent = "Hello World " + counter++;
  }, 1000)
`)
const htmlCode = ref(`<body><div id="app">Hello World 5!</div><script src="/index.js" /></body`)
let sandPackClient: SandpackClient

const handleHtmlEditorChange = () => {
  updateSandPack()
}
const handleEditorChange = () => {
  updateSandPack()
}

const updateSandPack = () => {
  sandPackClient.updateSandbox({
    files: {
      "/package.json": { code: JSON.stringify({
        main: "index.js"
      })},

      // Index file
      "/index.js": { code: `${code.value}`},
      
      // Main file
      "/index.html": { code: `${htmlCode.value}` },
    }
  });
}
onMounted(async () => {
  // Iframe selector or element itself
  const iframe = document.getElementById("iframe");
  
  // Files, environment and dependencies
  const content: SandboxSetup = {
    files: { 
      // We infer dependencies and the entry point from package.json 
      "/package.json": { code: JSON.stringify({
        main: "index.js"
      })},

      // Index file
      "/index.js": { code: `${code.value}`},
      
      // Main file
      "/index.html": { code: `${htmlCode.value}` },
    },
    entry: '/index.html', 
  };
  
  // Optional options
  const options: ClientOptions = {
  };
  
  // Properly load and mount the bundler
  sandPackClient = await loadSandpackClient(
    iframe, 
    content, 
    options
  );
})
</script>

<template>
  <main class="container">
    <div class="column">
      <textarea class="editor" v-model="htmlCode" @input="handleHtmlEditorChange" />
    </div>
    <div class="column">
      <textarea class="editor" v-model="code" @input="handleEditorChange" />
    </div>
    <div class="column">
      <iframe id="iframe" frameborder="0" height="100%" width="100%" />
    </div>
  </main>
  
</template>

<style scoped>
iframe {
  height:100%;
  width:100%;
}

.container {
  display: flex;
  flex-wrap: wrap;
  align-items: stretch;
  border-radius: 4px;
  overflow: hidden;
  position: relative;
  background-color: #EFEFEF;
  border: 1px solid #EFEFEF;
  gap: 1px;
}

.column {
  flex: 1 1 0%;
  display: flex;
  flex-direction: column;
  background: white;
  overflow: auto;
  min-height: 500px;
}

.editor {
  width: 100vw;
  height: 100vh;
}
</style>
