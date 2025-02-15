<script lang="ts">
  let mode: "preview" | "auto" = "preview";
  let refreshCount = 0;
  let refreshLimit = 0;
  let url = "";
  let status = "";
  let iframeSrc = "";
  
  let refreshInterval: number;

  function setMode(selectedMode: "preview" | "auto") {
    mode = selectedMode;
    if (refreshInterval) clearInterval(refreshInterval);
  }

  function loadWebsite() {
    if (!url.startsWith("http://") && !url.startsWith("https://")) {
      alert("Please enter a valid URL");
      return;
    }
    iframeSrc = url;
    status = "";
    
    if (mode === "auto") {
      startAutoRefresh();
    }
  }

  function startAutoRefresh() {
    refreshCount = 0;
    const limit = Number(refreshLimit) || Infinity;
    
    refreshInterval = setInterval(() => {
      if (refreshCount >= limit) {
        clearInterval(refreshInterval);
        status = "Refresh limit reached";
        return;
      }
      
      iframeSrc += "";
      refreshCount++;
      status = `Refreshed ${refreshCount} times`;
    }, 5000);
  }

  import { onDestroy } from 'svelte';
  onDestroy(() => {
    if (refreshInterval) clearInterval(refreshInterval);
  });
</script>

<main class="bg-gray-100 min-w-screen min-h-screen flex items-center justify-center">
  <div class="w-3/5 h-3/5 p-6 bg-white rounded-lg shadow-lg">
    <h1 class="text-3xl font-bold mb-6 text-center text-blue-700">Web Preview</h1>
    
    <div class="flex justify-between items-center mb-4">
      <input
        type="text"
        bind:value={url}
        class="flex-1 px-4 py-2 border rounded-md focus:ring-2 focus:ring-blue-500"
        placeholder="Please enter a website URL"
      />
      <button
        on:click={loadWebsite}
        class="ml-4 bg-blue-500 text-white px-4 py-2 rounded-md hover:bg-blue-600"
      >
        Loading Website
      </button>
    </div>

    <div class="flex justify-center space-x-4 mb-4">
      <button
        on:click={() => setMode('preview')}
        class:bg-green-700={mode === 'preview'}
        class:text-white={mode === 'preview'}
        class="mode-btn px-4 py-2 rounded-md border"
      >
        View
      </button>
      <button
        on:click={() => setMode('auto')}
        class:bg-green-700={mode === 'auto'}
        class:text-white={mode === 'auto'}
        class="mode-btn px-4 py-2 rounded-md border"
      >
        Auto Refresh
      </button>
    </div>

    {#if mode === 'auto'}
      <div class="mb-4">
        <label class="block text-gray-700 mb-2" for="refreshLimit">Refresh count:</label>
        <input
          id="refreshLimit"
          type="number"
          bind:value={refreshLimit}
          class="w-full px-4 py-2 border rounded-md"
          min="0"
          placeholder="Enter the number of refreshes"
        />
      </div>
    {/if}

    <div class="iframe-container bg-gray-700">
      <iframe src={iframeSrc} title="Website Preview" 
        class="w-full h-full"
      ></iframe>
    </div>

    {#if status}
      <p class="text-center mt-4 text-gray-700">{status}</p>
    {/if}
  </div>
</main>

<style>
  .iframe-container {
    position: relative;
    width: 100%;
    padding-top: 56.25%;
    border-radius: 12px;
    overflow: hidden;
  }

  .iframe-container iframe {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    border: none;
  }

</style>