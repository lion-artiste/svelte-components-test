<script lang="ts">
import {onMount, onDestroy } from 'svelte';
import { Play, Square, Maximize, Minus, Plus } from "lucide-svelte";
import Interactable from "./Interactable.svelte";

export let tempo: number = 120;
$: interval = 60000 / tempo;
export let min = 30;
export let max = 360;

let ticks: boolean[] = [true, false, false, false]; // true if tick accentuated
let curTick = -1;

let playing = false;
let ticking = false;
$: if (playing && !ticking) tick();

let fullscreen = false;

function tick() {
  if (!playing) {curTick = -1; ticking = false; return;}
  setTimeout(tick,interval);
  ticking = true;
  curTick++;
  if (curTick >= ticks.length) curTick = 0;
}

function changeTempo(amount) {
  tempo += amount;
  if (tempo > max) tempo = max;
  if (tempo < min) tempo = min;
}

onDestroy(() => {playing = false; ticking = false;})
</script>

<div class="w-full h-full grid grid-cols-6 grid-rows-5 bg-slate-600">
  <!-- Top ticks display -->
  <Interactable class={`row-start-1 col-start-1 col-end-7 bg-slate-800 flex flex-row gap-x-1 items-center justify-between relative ${(fullscreen) ? "row-end-7" : "row-end-4"}`} on:swipeY={() => fullscreen = !fullscreen}>
    <div class="basis-1/6 flex flex-col items-center justify-center text-slate-500 hover:text-white" on:click={() => {ticks.pop(), ticks = ticks;}}><Minus/></div>
    {#each ticks as tick,i}
    <div class="h-full flex flex-col justify-center items-center px-1 cursor-pointer" on:click={() => ticks[i] = !ticks[i]}>
      <div class="tick" class:accent={tick} class:playing={playing && (i == curTick)}></div>
    </div>
    {/each}
    <div class="basis-1/6 flex flex-col items-center justify-center text-slate-500 hover:text-white transition-colors" on:click={() => {ticks.push(false), ticks = ticks;}}><Plus/></div>
    <!--<button class="absolute bottom-1 right-1 text-slate-400 hover:text-white" on:click={() => fullscreen = !fullscreen}><Maximize size=18/></button>-->
  </Interactable>

  <!-- Play button -->
  {#if !fullscreen}
  <button class="row-start-4 row-end-6 col-start-1 col-end-3 flex flex-col items-center justify-center border-r-2 border-r-slate-700 text-white cursor-pointer hover:bg-slate-700 transition-colors" on:click={() => playing = !playing}>
    {#if playing}
    <Square size=40/>
    {:else}
    <Play size=40/>
    {/if}
  </button>

  <div class="row-start-4 row-end-6 col-start-4 col-end-6 bg-slate-700 text-white flex flex-col items-center justify-center text-2xl">{tempo}</div>

  <button class="row-start-4 row-end-5 col-start-3 col-end-4 flex flex-col items-center justify-center border-b-2 border-b-slate-700 text-white" on:click={() => changeTempo(-1)}>-1</button>
  <button class="row-start-5 row-end-6 col-start-3 col-end-4 flex flex-col items-center justify-center text-white" on:click={() => changeTempo(-10)}>-10</button>
  <button class="row-start-4 row-end-5 col-start-6 col-end-7 flex flex-col items-center justify-center border-b-2 border-b-slate-700 text-white" on:click={() => changeTempo(+1)}>+1</button>
  <button class="row-start-5 row-end-6 col-start-6 col-end-7 flex flex-col items-center justify-center text-white" on:click={() => changeTempo(+10)}>+10</button>
  {/if}
</div>

<style lang="postcss">
.tick {
  width: 4px;
  height: 40%;
  @apply bg-slate-300;
  transition: transform 0.1s ease-out, height 0.3s ease-out
}

.tick.accent {
  background-color: white;
  height: 70%;
}

.tick.playing {
  box-shadow: 0px 0px 2px red;
  transform: scale(1.05);
  @apply bg-red-500
}
</style>