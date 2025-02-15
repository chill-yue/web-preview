<script lang="ts">
  import { createEventDispatcher } from "svelte";
  const dispatch = createEventDispatcher();

  export let url = "";
  export let onLoadWebsite: () => void;

  function handleSubmit(e: Event) {
    e.preventDefault();
    // 确保url值已更新
    if (url.trim() === "") {
      alert("请输入有效的网站URL");
      return;
    }
    // 添加http前缀（如果没有）
    if (!url.startsWith("http://") && !url.startsWith("https://")) {
      url = "https://" + url;
    }
    dispatch("urlUpdate", url);
    onLoadWebsite();
  }

  function handleUrlInput(e: Event) {
    const input = e.target as HTMLInputElement;
    url = input.value;
    dispatch("urlUpdate", url);
  }
</script>

<header class="w-full mb-8">
  <h1 class="text-4xl font-light mb-8 text-center text-gray-900">
    Web Preview
  </h1>
  <form
    on:submit={handleSubmit}
    class="flex justify-between items-center gap-4"
  >
    <div class="relative flex-1">
      <input
        type="text"
        bind:value={url}
        on:input={handleUrlInput}
        class="w-full px-4 h-12 rounded-xl border border-gray-200 focus:outline-none focus:ring-2 focus:ring-gray-200 transition-all duration-300 ease-in-out text-gray-700 bg-gray-50"
        placeholder="请输入网站URL"
      />
    </div>
    <button
      type="submit"
      class="h-12 px-6 rounded-xl bg-gray-900 text-white hover:bg-gray-800 transition-colors duration-300 ease-in-out font-medium"
    >
      预览
    </button>
  </form>
</header>
