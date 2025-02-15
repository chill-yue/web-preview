<script lang="ts">
  import Header from "./components/Header.svelte";
  import ControlPanel from "./components/ControlPanel.svelte";
  import PreviewPanel from "./components/PreviewPanel.svelte";

  let url = "";
  let status = "";
  let iframeSrc = "";
  let autoRefresh = false;
  let refreshTimeout: number;

  function handleUrlUpdate(event: CustomEvent<string>) {
    url = event.detail;
    console.log("URL updated:", url);
  }

  function loadWebsite() {
    console.log("Loading website with URL:", url);
    if (!url) {
      console.warn("URL is empty");
      return;
    }

    iframeSrc = url;
    status = "正在加载...";

    const iframe = document.querySelector("iframe");
    if (iframe) {
      iframe.onerror = () => {
        status = "由于浏览器的安全策略，某些网站可能无法在iframe中预览。";
      };
      iframe.onload = () => {
        status = "加载成功";
      };
    }

    if (autoRefresh) {
      startAutoRefresh();
    }
  }

  function handleAutoRefreshChange(enabled: boolean) {
    autoRefresh = enabled;
    if (enabled && url) {
      startAutoRefresh();
    } else if (!enabled && refreshTimeout) {
      clearTimeout(refreshTimeout);
      refreshTimeout = 0;
    }
  }

  function startAutoRefresh() {
    if (refreshTimeout) {
      clearTimeout(refreshTimeout);
    }

    function refreshIframe() {
      iframeSrc = `${url}?timestamp=${new Date().getTime()}`;
      const randomInterval = Math.floor(Math.random() * 10 + 5) * 1000;
      refreshTimeout = setTimeout(refreshIframe, randomInterval);
    }

    refreshIframe();
  }

  import { onDestroy } from "svelte";
  onDestroy(() => {
    if (refreshTimeout) clearTimeout(refreshTimeout);
  });

  $: {
    console.log("URL changed:", url);
  }
</script>

<main class="min-h-screen bg-gradient-to-b from-gray-50 to-white">
  <div class="container mx-auto max-w-5xl px-4 py-8">
    <Header {url} onLoadWebsite={loadWebsite} on:urlUpdate={handleUrlUpdate} />
    <ControlPanel {autoRefresh} onAutoRefreshChange={handleAutoRefreshChange} />
    <PreviewPanel {iframeSrc} {status} />
  </div>
</main>

<style>
  :global(body) {
    margin: 0;
    font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto,
      Oxygen-Sans, Ubuntu, Cantarell, "Helvetica Neue", sans-serif;
  }
</style>
