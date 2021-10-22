<script context="module">
  const observers = new Map();
  const entryMap = new WeakMap();

  function makeObserver(rootMargin) {
    return new IntersectionObserver(
      (entries, observer) => {
        for (let entry of entries) {
          let entryData = entryMap.get(entry.target);
          if (!entryData) {
            observer.unobserve(entry.target);
            continue;
          }

          entryData.callback(entry);
          if (entry.isIntersecting && !entryData.hideOnExit) {
            entryMap.delete(entry.target);
            observer.unobserve(entry.target);
          }
        }
      },
      { rootMargin }
    );
  }

  function listen(rootMargin, element, hideOnExit, callback) {
    let observer = observers.get(rootMargin);
    if (!observer) {
      observer = makeObserver(rootMargin);
    }

    entryMap.set(element, { callback, hideOnExit });
    observer.observe(element);
    return () => {
      observer.unobserve(element);
      entryMap.delete(element);
    };
  }
</script>

<script>
  import { createEventDispatcher } from "svelte";
  const dispatch = createEventDispatcher();

  export let visible = false;
  export let height = undefined;
  export let rootMargin = "20%";
  /** If true, hide the element again when it leaves the viewport. */
  export let hideOnExit = false;

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

    let destroy = listen(rootMargin, node, hideOnExit, ({ isIntersecting }) => {
      visible = isIntersecting;
      if (isIntersecting) {
        dispatch("visible");
      } else if (hideOnExit) {
        dispatch("invisible");
      }
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
