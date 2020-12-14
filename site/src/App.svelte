<script>
  import { fade } from "svelte/transition";
  import LazyLoad from "../../src/LazyLoad.svelte";

  let visible = false;
</script>

<div style="position:fixed;top:0">Visible: {visible}</div>
<div style="height:150vh" />

<LazyLoad rootMargin="0px" height="4rem" bind:visible>
  <h1 in:fade={{ duration: 2000 }}>Hello!</h1>
</LazyLoad>

<LazyLoad rootMargin="0px" height="4rem" hideOnExit={true}>
  <h1 in:fade={{ duration: 2000 }}>
    This one hides when it leaves the viewport
  </h1>
</LazyLoad>

<LazyLoad rootMargin="0px" height="4rem">
  <h1 in:fade={{ duration: 2000 }}>Hello Again!</h1>
</LazyLoad>
