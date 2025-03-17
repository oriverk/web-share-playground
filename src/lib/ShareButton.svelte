<script lang="ts">
  import { onMount } from "svelte";

  interface Props {
    title?: string;
    text?: string;
    url?: string;
    className?: string;
  }

  let { title, text, url, className }: Props = $props();

  let shareIcon = $state('');
  let isShareSupported = $state(false);

  onMount(() => {
    isShareSupported = navigator.share !== undefined;
    if (/android/i.test(navigator.userAgent)) {
      shareIcon = "M12 3v2h5.59L4 18.59 5.41 20 18 7.41V13h2V3h-8z"; // Android風アイコン
    } else if (/iPhone|iPad|iPod/i.test(navigator.userAgent)) {
      shareIcon = "M12 4v12M8 8l4-4 4 4M4 12h16v8H4z"; // iOS風アイコン
    } else {
      shareIcon = "M4 12h16v8H4z M12 4v12M8 8l4-4 4 4"; // 汎用アイコン
    }
  });

  async function sharePage() {
    if (isShareSupported) {
      try {
        await navigator.share({
          title: title ?? document.title,
          text: text ?? '',
          url: url ?? window.location.href
        });
      } catch (error) {
        console.error("シェアに失敗しました", error);
      }
    }
  }
</script>

<button
  onclick={sharePage}
  class="btn btn-primary flex items-center gap-2"
  disabled={!isShareSupported}
>
  <svg xmlns="http://www.w3.org/2000/svg" class="w-5 h-5" viewBox="0 0 24 24" fill="currentColor">
    <path d={shareIcon} />
  </svg>
  シェア
</button>
<div>
  {navigator.userAgent}
</div>
