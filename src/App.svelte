<script lang="ts">
  import Canvas from "./Canvas.svelte";
  import { Resizer } from "./lib/components/resizer";

  let height = $state(200);
  let width = $state(200);
</script>

<main>
  <Resizer
    {height}
    {width}
    onChange={(newWidth, newHeight) => {
      height = newHeight;
      width = newWidth;
    }}
  >
    <Canvas {height} {width}></Canvas>
  </Resizer>
</main>

<style>
  main {
    padding: 40px;
  }
</style>
