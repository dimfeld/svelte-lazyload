<script>
  import { fade } from 'svelte/transition';
  import LazyLoad from '../../src/LazyLoad.svelte';
  import { onMount } from 'svelte';

  let visible = false;
</script>

<div style="position:fixed;top:0">Visible: {visible}</div>
<div style="height:150vh" />

<LazyLoad height="4rem" bind:visible>
  <h1 in:fade={{ duration: 2000 }}>Hello!</h1>
</LazyLoad>
