<script context="module">
  const observers = new Map();
  const entryMap = new WeakMap();

  function makeObserver(rootMargin) {
    return new IntersectionObserver(
      (entries, observer) => {
        for (let entry of entries) {
          if (entry.intersectionRatio > 0) {
            let entryData = entryMap.get(entry.target);
            if (entryData) {
              entryData(entry);
              entryMap.delete(entry.target);
            }
            observer.unobserve(entry.target);
          }
        }
      },
      { rootMargin }
    );
  }

  function listen(rootMargin, element, callback) {
    let observer = observers.get(rootMargin);
    if (!observer) {
      observer = makeObserver(rootMargin);
    }

    entryMap.set(element, callback);
    observer.observe(element);
    return () => {
      observer.unobserve(node);
      entryMap.delete(node);
    };
  }
</script>

<script>
  import { createEventDispatcher } from "svelte";
  const dispatch = createEventDispatcher();

  export let visible = false;
  export let height = undefined;
  export let rootMargin = "20%";

  export let id = undefined;
  export let style = undefined;
  let classNames = undefined;
  export { classNames as class };

  function observe(node) {
    if (
      visible ||
      typeof window === "undefined" ||
      !window.IntersectionObserver
    ) {
      // Handle when visible is externally set to true, or InterserctionObserver is not available.
      dispatch("visible");
      visible = true;
      return {};
    }

    let destroy = listen(rootMargin, node, () => {
      dispatch("visible");
      visible = true;
    });

    return {
      destroy,
    };
  }

  $: heightStyle = height ? `height:${height}` : undefined;
  $: styleAttr = [heightStyle, style].filter(Boolean).join(";");
</script>

<div {id} style={styleAttr} class={classNames} use:observe>
  {#if visible}
    <slot />
  {:else}
    <!-- Zero-width space character -->
    &#8203;
  {/if}
</div>
