<script lang="ts">
import { writable } from "svelte/store";
import { spring } from "svelte/motion";
import { fly } from 'svelte/transition';
import { createEventDispatcher } from "svelte";
import { ArrowRightSquare } from 'lucide-svelte';
import { Trash2, Calendar, Clock7 } from 'lucide-svelte';

interface TASK {
  name: string,
  date: Date
}

export let task;
export let morePanel = false;

let taskWidth;

let dispatch = createEventDispatcher();

let xMouseDown = 0;
let xMouseCurrent = 0;
let dragged = false;
let xMovement = spring(0);
xMovement.stiffness = 0.3;
xMovement.damping = 0.9;
xMovement.precision = 0.005;
$: xMovement.set((dragged) ? xMouseCurrent - xMouseDown : (taskVisible) ? 0 : $xMovement);

let taskVisible = true;
$: dragLimit = (taskWidth) ? Math.min(taskWidth/6, 150) : 150;

function taskDown(xValue) {
  dragged = true;
  xMouseDown = xValue;
  xMouseCurrent = xValue;
}

function taskMove(xValue) {
  if (!dragged) return;
  xMouseCurrent = xValue;
}

function taskUp() {
  dragged = false;
  if (Math.abs($xMovement) > dragLimit) {
    taskVisible = false;
  }
}

function dispatchEnd() {
  if ($xMovement > 0) {
    dispatch("reported",{task})
  } else {
    dispatch("deleted",{task})
  }
}
</script>

<!-- Main panel -->
<div bind:offsetWidth={taskWidth} class="relative h-[80px] flex flex-row shadow-inner select-none">

  <!-- Backgrounds -->
  <div class:hidden={$xMovement < 0} class="w-full bg-yellow-400 flex flex-row items-center justify-start text-black">
    <div class="flex flex-row items-center gap-x-2 transition-transform" class:scale-110={$xMovement > dragLimit}>
      <ArrowRightSquare class="ml-4"/>
      {#if taskWidth > 400}
      <div>Reporter</div>
      {/if}
    </div>
  </div>
  <div class:hidden={$xMovement > 0} class="w-full bg-red-500 flex flex-row items-center justify-end text-white">
    <div class="flex flex-row items-center gap-x-2 transition-transform" class:scale-110={$xMovement < -dragLimit}>
      {#if taskWidth > 400}
      <div>Supprimer</div>
      {/if}
      <Trash2 class="mr-4"/>
    </div>
  </div>

  <!-- Main task -->
  {#if taskVisible}
  <div style:left={`${$xMovement}px`} class="bg-white absolute t-0 w-full h-full shadow-md flex flex-col items-center justify-center gap-y-1 cursor-pointer" on:mousedown={(event) => {taskDown(event.clientX)}} on:mousemove={(event) => {taskMove(event.clientX)}} on:mouseup={taskUp} transition:fly="{{ x: ($xMovement < 0) ? -500 : 500, duration: 200 }}" on:outroend="{dispatchEnd}">
    <div class="text-xl">{task?.name}</div>
    {#if task?.date}
    <div class="text-sm flex flex-row gap-x-6">
      <div class="flex flex-row gap-x-1 items-center">
        <div><Calendar size=16/></div>
        <div>{`${task.date.getDate()} / ${task.date.getMonth() + 1} / ${task.date.getFullYear()}`}</div>
      </div>
      <div class="flex flex-row gap-x-1 items-center">
        <div><Clock7 size=16/></div>
        <div>{`${task.date.getHours()}:${task.date.getMinutes()}`}</div>
      </div>
    </div>
    {/if}
  </div>
  {/if}
</div>

<!-- Configure panel-->
<div></div>