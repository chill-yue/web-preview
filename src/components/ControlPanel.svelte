<script lang="ts">
  export let autoRefresh = false;
  export let onAutoRefreshChange: (enabled: boolean) => void;

  function handleAutoRefreshChange() {
    onAutoRefreshChange(autoRefresh);
  }
</script>

<div class="control-panel">
  <label class="flex items-center justify-center gap-2 cursor-pointer">
    <input
      type="checkbox"
      bind:checked={autoRefresh}
      on:change={handleAutoRefreshChange}
      class="w-5 h-5 rounded border-gray-300 text-gray-900 focus:ring-gray-200 transition-all duration-300 ease-in-out"
    />
    <span class="text-gray-700">自动刷新</span>
  </label>
</div>
