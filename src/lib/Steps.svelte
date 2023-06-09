<script lang="ts">
  import {tweened} from "svelte/motion";
  import { cubicOut } from 'svelte/easing';
  import Step from './Step.svelte';
  let nbSteps = Object.keys($$slots).length;
  let curStep = 1;

  export let progressBar = true;
  export let bgColor = "multi";
  let COLORS = ["#177e89", "#084c61","#db3a34","#ffc857","#323031"]
  let curColor = 0;
  const getColor = () => {
    let color = COLORS[curColor];
    curColor = (curColor + 1) % COLORS.length;
    return color;
  }

  let width;

  let progressWidth = tweened((curStep) / (nbSteps) * 100, {
		duration: 400,
		easing: cubicOut
	});
  $: if(curStep && nbSteps) progressWidth.set((curStep) / (nbSteps) * 100);

  function next() {
    if (curStep < nbSteps) curStep = curStep + 1;
  }

  function prev() {
    if (curStep > 1) curStep = curStep - 1;
  }
</script>

<div class="h-full w-full overflow-hidden" style:background-color={bgColor} bind:offsetWidth={width}>

  {#if $$slots.step2}
  <Step pos={(curStep > 2) ? "left" : (curStep < 2) ? "right" : "center"} bgColor={(bgColor == "multi") ? getColor() : bgColor} on:next={next} on:prev={prev}>
    <slot name="step2"></slot>
  </Step>
  {/if}

  {#if $$slots.step1}
  <Step pos={(curStep > 1) ? "left" : (curStep < 1) ? "right" : "center"} bgColor={(bgColor == "multi") ? getColor() : bgColor} on:next={next} on:prev={prev}>
    <slot name="step1"></slot>
  </Step>
  {/if}

  {#if progressBar}
  <div class="absolute z-10 h-[4px] w-full bottom-0 left-0 bg-black/20">
    <div class="bg-white absolute left-0 top-0 h-full" style:width={`${$progressWidth}%`}></div>
  </div>
  {/if}

</div>