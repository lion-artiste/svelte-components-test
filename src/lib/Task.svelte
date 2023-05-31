<script lang="ts">
import { writable } from "svelte/store";
import { spring } from "svelte/motion";
import { fly } from 'svelte/transition';
import { createEventDispatcher } from "svelte";
interface TASK {
  name: string,

}

export let task;
export let morePanel = false;

let dispatch = createEventDispatcher();

let xMouseDown = 0;
let xMouseCurrent = 0;
let dragged = false;
let xMovement = spring(0);
xMovement.stiffness = 0.3;
xMovement.damping = 0.9;
xMovement.precision = 0.005;
$: xMovement.set((dragged) ? xMouseCurrent - xMouseDown : 0);

let direction = "neutral";
$: if (taskVisible && dragged) {
  direction = (xMovement > 0) ? "right" : (xMovement < 0) ? "left" : "neutral";
}

let taskVisible = true;


function taskMove(xValue) {
  if (!dragged) return;
  xMouseCurrent = xValue;
  if (Math.abs($xMovement) > 200) {
    taskVisible = false;
  }
  if ($xMovement > 0) {direction = "right"}
  else if ($xMovement < 0) {direction = "left"}
}
</script>

<!-- Main panel -->
<div class="relative h-[80px] flex flex-row">

  <!-- Backgrounds -->
  <div class:hidden={direction == "left"} class="w-full bg-yellow-500"></div>
  <div class:hidden={direction == "right"} class="w-full bg-red-500"></div>

  <!-- Main task -->
  {#if taskVisible}
  <div style:left={`${$xMovement}px`} class="bg-white absolute t-0 w-full h-full shadow-md" on:mousedown={(event) => {dragged = true;xMouseDown = event.clientX; xMouseCurrent = event.clientX;}} on:mousemove={(event) => {taskMove(event.clientX)}} on:mouseup={(event) => {dragged = false;}} transition:fly="{{ x: (direction == "left") ? -500 : 500, duration: 200 }}" on:outroend="{() => (direction == "right") ? dispatch("reported", {task}) : dispatch("deleted", {task})}">
    <div class="">{task?.name}</div>
  </div>
  {/if}
</div>

<!-- Configure panel-->
<div></div>