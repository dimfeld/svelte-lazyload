<script>
  import { createEventDispatcher } from 'svelte';
  const dispatch = createEventDispatcher();

  export let visible = false;
  export let height = undefined;
  export let rootMargin = '20%';

  function observe(node) {
    if (visible || !window.IntersectionObserver) {
      dispatch('visible');
      visible = true;
      return {};
    }

    let observer = new IntersectionObserver(
      (entries) => {
        if (entries[0].isIntersecting) {
          visible = true;
          dispatch('visible');

          // Unobserve since there's nothing left to do.
          observer.unobserve(node);
          observer = null;
        }
      },
      { rootMargin }
    );

    observer.observe(node);

    return {
      destroy() {
        if (observer) observer.unobserve(node);
      },
    };
  }

  $: heightStyle = height ? `height:${height}` : undefined;
</script>

<div style={heightStyle} use:observe>
  {#if visible}
    <slot />
  {:else}
    <!-- Zero-width space character -->
    &#8203;
  {/if}
</div>
