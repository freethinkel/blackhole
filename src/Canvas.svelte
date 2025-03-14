<script lang="ts">
  import { onMount } from "svelte";
  import { draw } from "svelte/transition";

  let canvasEl: HTMLCanvasElement;

  interface Props {
    width?: number;
    height?: number;
  }
  const { height, width }: Props = $props();

  let isActive = true;

  const drawBlackHole = (
    ctx: CanvasRenderingContext2D,
    width: number,
    height: number,
  ) => {
    const imgData = ctx.getImageData(0, 0, width, height);

    for (let col = 0; col < height; col++) {
      for (let row = 0; row < width; row++) {
        const x = col;
        const y = row;

        const w = width;
        const h = height;

        const cx = (2 * x - w) / h;
        const cy = (2 * y - h) / h;
        let d = Math.sqrt(cx * cx + cy * cy);
        d -= 0.5;
        d += (0.01 * h) / (2 * (y - x) + h - w);
        d = Math.abs(d);
        if (d < 1e-6) d = 1e-6;
        d = 0.1 / d;

        const color = (255 * d) / (1 + d);

        const i = (y * width + x) * 4;
        imgData.data[i + 0] = color;
        imgData.data[i + 1] = color * 0.6;
        imgData.data[i + 2] = 0;
        imgData.data[i + 3] = 255;
      }
    }

    ctx.putImageData(imgData, 0, 0);
  };

  const loop = () => {
    if (!isActive) {
      return;
    }

    if (!canvasEl) {
      return;
    }

    const ctx = canvasEl.getContext("2d")!;
    const height = canvasEl.height;
    const width = canvasEl.width;

    drawBlackHole(ctx, width, height);
  };

  const render = () => {
    loop();
    window.requestAnimationFrame(render);
  };

  onMount(() => {
    window.requestAnimationFrame(render);
  });
</script>

<canvas class="wrapper" bind:this={canvasEl} {width} {height}> </canvas>

<style lang="css">
  .wrapper {
    height: 100%;
    width: 100%;
    border: none;
    margin: 0;
    padding: 0;
  }
</style>
