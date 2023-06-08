<script lang="ts">
import { writable } from "svelte/store";
import { spring } from "svelte/motion";
import { fly } from 'svelte/transition';
import { createEventDispatcher } from "svelte";
import { ArrowRightSquare } from 'lucide-svelte';
import { Trash2, Calendar, Clock7, Check } from 'lucide-svelte';

interface TASK {
  name: string,
  date: Date,
  id: number,
  open: boolean,
  dateDone: Date | null
}

export let task: TASK;
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

let open = false;

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
    dispatch("completed",{task})
  } else {
    dispatch("deleted",{task})
  }
}
</script>

<!-- Main panel -->
<div bind:offsetWidth={taskWidth} class="relative h-[80px] flex flex-row shadow-inner select-none">

  <!-- Backgrounds -->
  <div class:hidden={$xMovement < 0} class="w-full bg-green-400 flex flex-row items-center justify-start text-black">
    <div class="flex flex-row items-center gap-x-2 transition-transform" class:scale-110={$xMovement > dragLimit}>
      <Check class="ml-4"/>
      {#if taskWidth > 400}
      <div>Compléter</div>
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
  <div style:left={`${$xMovement}px`} class="bg-white absolute t-0 w-full h-full shadow-md flex flex-col items-center justify-center gap-y-1 cursor-pointer" on:mousedown={(event) => {taskDown(event.clientX)}} on:mousemove={(event) => {taskMove(event.clientX)}} on:mouseup={taskUp} out:fly="{{ x: ($xMovement < 0) ? -500 : 500, duration: 200 }}" on:outroend="{dispatchEnd}">
    <div class="text-xl" class:text-inactive={!task.open}>{task?.name}</div>
    {#if task?.open}
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
    {:else} <!--Task closed-->
      <div class="flex flex-row gap-x-1 text-green-500">
        <Check/>
        {#if task?.dateDone}
        <div>Complétée le {`${task.dateDone.getDate()} / ${task.dateDone.getMonth() + 1} / ${task.dateDone.getFullYear()}`}</div>
        {/if}
      </div>
    {/if}
  </div>
  {/if}
</div>

<!-- Configure panel-->
{#if open}
<div>
  <div class="join">
    <input type="date" id="start" name="trip-start" value="2018-07-22" min="2018-01-01" max="2018-12-31">
  </div>
</div>
{/if}

<style>
.text-inactive {
  text-decoration-line: line-through;
  color: #bbb
}
</style>