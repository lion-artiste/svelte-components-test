<script lang="ts">
  import {tweened} from "svelte/motion";
  import { cubicOut } from 'svelte/easing';
  let nbSteps = 2;
  let curStep = 1;
  export let bgColor = "#cc0000";

  let width;

  let progressWidth = tweened(0, {
		duration: 300,
		easing: cubicOut
	});
  $: if (width) progressWidth.set((curStep) / (nbSteps) * width);

  function next() {
    curStep = (curStep + 1) % nbSteps
  }

  function prec() {
    curStep = (curStep == 0) ? nbSteps - 1 : curStep - 1;
  }
</script>

<div class="h-full w-full overflow-hidden" style:background-color={bgColor} bind:offsetWidth={width}>

  <div class="w-full h-full absolute top-0 left-0 transition-left bg-green-500 flex flex-row gap-x-10" style:left={(curStep <= 1) ? "0px" : `-${width}px`}>
    <div class="grow cursor-pointer" on:click={prec}></div>
    <div class="flex flex-col justify-center gap-y-10">
      <div class="grow"></div>
      <div class="flex flex-col items-center justify-center gap-y-3">
        <input class="input" type="text" id="name" name="name" required minlength="4" maxlength="8" size="10">
        <input class="input" type="text" id="name" name="name" required minlength="4" maxlength="8" size="10">
      </div>
      <div class="grow"></div>
    </div>
    <div class="grow cursor-pointer" on:click={next}></div>
  </div>

  <div class="w-full h-full absolute transition-left bg-blue-500 flex flex-col items-center justify-center gap-y-1" style:left={(curStep == 0) ? "0px" : `-${width}px`}>
    <button on:click={() => next()}>Passer au suivant</button>
  </div>

  <div class="absolute bottom-0 left-0 w-full h-[5px] bg-transparent z-10">
    <div class="h-full bg-white absolute left-0" style:width={`${$progressWidth}px`}></div>
  </div>

</div>

<style>
.transition-left {
  transition: left 0.3s ease-out;
}
</style>