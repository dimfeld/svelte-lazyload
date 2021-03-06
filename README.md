# svelte-lazyload

This is a small component that uses an `IntersectionObserver` to delay loading some element of a page until it is about to enter the viewport. It exposes a property `visible` indicating if the element has been created, and also fires an event named `visible` when it loads the element.

```svelte
<script>
  import { fade } from 'svelte/transition';
  import LazyLoad from '@dimfeld/svelte-lazyload';
  let visible = false;
</script>

<div style="position:fixed;top:0">
  Visible: {visible}
</div>
<div style="height:150vh">

</div>

<LazyLoad height="4rem" bind:visible on:visible={() => console.log('visible!')}>
  <h1 in:fade={{duration: 2000 }}>Hello!</h1>
</LazyLoad>
```

Try it in the [Svelte REPL](https://svelte.dev/repl/9a2f40ce30b34337be76df5e9be168e8)!.
