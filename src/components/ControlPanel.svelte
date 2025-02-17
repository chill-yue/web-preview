<script lang="ts">
  export let autoRefresh = false;
  export let onAutoRefreshChange: (enabled: boolean, mode?: string) => void;

  let refreshMode = "fast";
  let countdown = 0;
  let countdownInterval: number | null = null;
  let customMinInterval = 5;
  let customMaxInterval = 10;
  let isRunning = false;

  // 定义不同模式的时间间隔配置
  type IntervalConfig = { min: number; max: number };
  type ModeIntervals = {
    [key: string]: IntervalConfig;
  };

  const modeIntervals: ModeIntervals = {
    fast: { min: 5, max: 10 },
    slow: { min: 20, max: 25 },
    custom: { min: customMinInterval, max: customMaxInterval },
  };

  // 获取当前模式的时间间隔
  function getCurrentIntervals(): IntervalConfig {
    if (refreshMode === "custom") {
      return { min: customMinInterval, max: customMaxInterval };
    }
    return modeIntervals[refreshMode];
  }

  // 生成随机间隔时间
  function generateInterval() {
    const { min, max } = getCurrentIntervals();
    return Math.floor(Math.random() * (max - min + 1)) + min;
  }

  // 重置定时器
  function resetTimer() {
    if (countdownInterval) {
      clearInterval(countdownInterval);
      countdownInterval = null;
    }
    if (isRunning && autoRefresh) {
      countdown = generateInterval();
      startCountdown();
    }
  }

  function handleAutoRefreshChange() {
    if (!autoRefresh) {
      stopRefresh();
    } else {
      // 仅通知状态改变，不启动刷新
      onAutoRefreshChange(autoRefresh, refreshMode);
    }
  }

  function handleRefreshModeChange() {
    if (autoRefresh) {
      resetTimer();
      onAutoRefreshChange(autoRefresh, refreshMode);
    }
  }

  function handleCustomIntervalChange() {
    if (customMinInterval > customMaxInterval) {
      customMaxInterval = customMinInterval;
    }
    if (autoRefresh && refreshMode === "custom") {
      resetTimer();
    }
  }

  function startRefresh() {
    if (!isRunning && autoRefresh) {
      isRunning = true;
      resetTimer();
    }
  }

  function stopRefresh() {
    isRunning = false;
    if (countdownInterval) {
      clearInterval(countdownInterval);
      countdownInterval = null;
    }
    countdown = 0;
  }

  function startCountdown() {
    if (countdownInterval) return;

    countdownInterval = setInterval(() => {
      countdown--;
      if (countdown <= 0) {
        countdown = generateInterval();
        onAutoRefreshChange(autoRefresh, refreshMode);
      }
    }, 1000);
  }

  $: countdownDisplay = countdown > 0 ? `${countdown}秒` : "";
</script>

<div class="control-panel flex items-center justify-start gap-6 p-2">
  <label class="flex items-center gap-2 cursor-pointer">
    <input
      type="checkbox"
      bind:checked={autoRefresh}
      on:change={handleAutoRefreshChange}
      class="w-5 h-5 rounded border-gray-300 text-gray-900 focus:ring-gray-200 transition-all duration-300 ease-in-out"
    />
    <span class="text-gray-700">自动刷新</span>
  </label>

  {#if autoRefresh}
    <button
      on:click={isRunning ? stopRefresh : startRefresh}
      class="px-6 py-1.5 rounded-xl font-medium text-sm transition-all duration-300 ease-in-out
        {isRunning
        ? 'bg-black text-white shadow-sm hover:shadow-md'
        : 'bg-black to-green-500 text-white shadow-sm hover:shadow-md'}"
    >
      {isRunning ? "停止" : "开始"}
    </button>

    <div class="flex items-center gap-6">
      <div class="flex items-center gap-4">
        <label class="flex items-center gap-2 cursor-pointer">
          <input
            type="radio"
            bind:group={refreshMode}
            value="fast"
            on:change={handleRefreshModeChange}
            class="w-4 h-4 text-black focus:ring-black focus:ring-2 transition-all duration-300 ease-in-out"
          />
          <span class="text-gray-700">快速</span>
        </label>
        <label class="flex items-center gap-2 cursor-pointer">
          <input
            type="radio"
            bind:group={refreshMode}
            value="slow"
            on:change={handleRefreshModeChange}
            class="w-4 h-4 text-black focus:ring-black focus:ring-2 transition-all duration-300 ease-in-out"
          />
          <span class="text-gray-700">慢速</span>
        </label>
        <label class="flex items-center gap-2 cursor-pointer">
          <input
            type="radio"
            bind:group={refreshMode}
            value="custom"
            on:change={handleRefreshModeChange}
            class="w-4 h-4 text-black focus:ring-black focus:ring-2 transition-all duration-300 ease-in-out"
          />
          <span class="text-gray-700">自定义</span>
        </label>
      </div>

      {#if refreshMode === "custom"}
        <div class="flex items-center gap-2 border-l pl-6 border-gray-200">
          <input
            type="number"
            bind:value={customMinInterval}
            on:change={handleCustomIntervalChange}
            min="1"
            class="w-16 h-8 px-2 border border-gray-300 rounded text-sm"
          />
          <span class="text-gray-600">-</span>
          <input
            type="number"
            bind:value={customMaxInterval}
            on:change={handleCustomIntervalChange}
            min="1"
            class="w-16 h-8 px-2 border border-gray-300 rounded text-sm"
          />
          <span class="text-gray-600">秒</span>
        </div>
      {/if}

      {#if countdownDisplay}
        <div class="text-gray-600 text-sm border-l pl-6 border-gray-200">
          下次刷新: {countdownDisplay}
        </div>
      {/if}
    </div>
  {/if}
</div>
