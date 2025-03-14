<script lang="ts">
  import type { Snippet } from "svelte";

  interface Props {
    children?: Snippet;
    height?: number;
    width?: number;
    onChange?: (width: number, height: number) => void;
  }

  const { children, height = 200, width = 200, onChange }: Props = $props();

  let initialSize = { width: 0, height: 0 };
  let wrapperEl: HTMLDivElement;

  const onMove = (event: MouseEvent) => {
    const rect = wrapperEl.getBoundingClientRect();
    const x = event.clientX - rect.left - initialSize.width;
    const y = event.clientY - rect.top - initialSize.height;

    onChange?.(initialSize.width + x, initialSize.height + y);
  };
  const onUp = () => {
    window.removeEventListener("mousemove", onMove);
    window.removeEventListener("mouseup", onUp);
  };

  const onDown = () => {
    window.addEventListener("mousemove", onMove);
    window.addEventListener("mouseup", onUp);
    const size = wrapperEl.getBoundingClientRect();

    initialSize = {
      width: size.width,
      height: size.height,
    };
  };
</script>

<div
  class="wrapper"
  style:--width="{width}px"
  style:--height="{height}px"
  bind:this={wrapperEl}
>
  {@render children?.()}
  <div class="resizer" onpointerdown={onDown}></div>
</div>

<style>
  .wrapper {
    position: relative;
    border: 1px solid red;
    display: flex;
    align-items: center;
    justify-content: center;
    height: var(--height);
    width: var(--width);
  }
  .resizer {
    position: absolute;
    bottom: 0;
    right: 0;
    height: 20px;
    width: 20px;
    background: red;
  }
</style>
