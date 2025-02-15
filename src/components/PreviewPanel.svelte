<script lang="ts">
  export let iframeSrc = "";
  export let status = "";
</script>

<div class="preview-container">
  <div
    class="iframe-wrapper bg-white rounded-2xl shadow-lg overflow-hidden transition-all duration-300 ease-in-out"
  >
    <iframe
      src={iframeSrc}
      title="Website Preview"
      class="w-full h-full border-none"
      sandbox="allow-same-origin allow-scripts allow-popups allow-forms"
      allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture"
    ></iframe>
  </div>
  {#if status}
    <p
      class="mt-4 text-sm text-gray-500 text-center transition-opacity duration-300"
    >
      {status}
    </p>
  {/if}
</div>

<style>
  .preview-container {
    width: 100%;
    padding: 1rem;
    height: 80vh;
    min-height: 600px;
  }

  .iframe-wrapper {
    position: relative;
    width: 100%;
    height: 100%;
    background: linear-gradient(to bottom, #fafafa, #ffffff);
  }

  .iframe-wrapper:hover {
    transform: translateY(-2px);
    box-shadow:
      0 20px 25px -5px rgba(0, 0, 0, 0.1),
      0 10px 10px -5px rgba(0, 0, 0, 0.04);
  }

  iframe {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
  }

  @media (max-height: 800px) {
    .preview-container {
      height: 70vh;
      min-height: 480px;
    }
  }
</style>
