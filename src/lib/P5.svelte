<script>
	import { onMount, createEventDispatcher } from 'svelte';
	import { tw, apply } from 'twind';

	// API properties
	export let project = undefined;
	export let target = undefined;
	export let sketch = undefined;
	export let debug = false;
	// Event generation
	const event = createEventDispatcher();
	const dispatch = {
	  ref() {
	    event('ref', target);
	  },
	  init() {
	    event('instance', project);
	  },
	};
	/**
	 * Creates a reference for the p5 instance to render within
	 * @param {HTMLElement} node
	 */
	function ref(node) {
	  target = node;
	  return {
	    destroy() {
	      target = undefined;
	    },
	  };
	}
	function augmentClasses(instance, classes) {
	  // eslint-disable-next-line no-return-assign
	  classes.forEach(([key, value]) => (instance[key] = value));
	  return instance;
	}
	/**
	 * When the client loads, create the p5 instance
	 */
	onMount(async () => {
	  const library = await import('p5');
	  const { default: p5 } = library;
	  const entries = Object.entries(library);
	  const nativeClasses = entries.filter(
	    ([key, value]) => value instanceof Function && key[0] !== '_' && key !== 'default',
	  );
	  if (debug) {
	    console.log('available p5 native classes', nativeClasses);
	  }
	  // eslint-disable-next-line new-cap
	  project = new p5((instance) => {
	    instance = augmentClasses(instance, nativeClasses);
	    if (debug) {
	      console.log('p5 instance', instance);
	    }
	    // Set up a global object to capture this instance.
	    // @ts-ignore
	    // eslint-disable-next-line no-underscore-dangle
	    window._p5Instance = instance;
	    return sketch(instance);
	  }, target);
	  // Initial event dispatching
	  dispatch.ref();
	  dispatch.init();
	});
</script>

<div use:ref  class={tw`w-full flex justify-center`} />