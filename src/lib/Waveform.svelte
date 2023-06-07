<script lang="ts">
import WaveSurfer from 'wavesurfer.js';
import { onMount, onDestroy } from "svelte";
import { Square, Play } from 'lucide-svelte';
import { createEventDispatcher } from "svelte";

let dispatch = createEventDispatcher();

function makeId(length) {
    let result = '';
    const characters = 'ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789';
    const charactersLength = characters.length;
    let counter = 0;
    while (counter < length) {
      result += characters.charAt(Math.floor(Math.random() * charactersLength));
      counter += 1;
    }
    return result;
}

let COLORS = ["blue", "red", "yellow", "green"];
let colorsTable = {
  "blue": {
    wave: "hsla(213, 76%, 55%, 1)",
    progress: "hsla(213, 75%, 40%, 1)",
    background: "hsla(213, 76%, 90%, 1)",
    button: "hsla(213, 76%, 55%, 1)"
  },
  "red": {
    wave: "hsla(0, 76%, 55%, 1)",
    progress: "hsla(0, 75%, 40%, 1)",
    background: "hsla(0, 76%, 90%, 1)",
    button: "hsla(0, 76%, 55%, 1)"
  },
  "yellow": {
    wave: "hsla(48, 76%, 55%, 1)",
    progress: "hsla(48, 75%, 40%, 1)",
    background: "hsla(48, 76%, 90%, 1)",
    button: "hsla(48, 76%, 55%, 1)",
  },
  "green": {
    wave: "hsla(112, 76%, 55%, 1)",
    progress: "hsla(112, 75%, 40%, 1)",
    background: "hsla(112, 76%, 90%, 1)",
    button: "hsla(112, 76%, 55%, 1)",
  }
}

let height;

let loaded = false;
export let id = makeId(20);

function getRandomColor() {
  return COLORS[Math.floor(Math.random()*COLORS.length)];
}

export let playing = false;
$: if (playing) {
  wavesurfer?.play();
  dispatch("play",id);
} else {
  wavesurfer?.stop();
  dispatch('stop',id);
}

export let color: typeof COLORS[number] = getRandomColor();
export let audio;

let error = false;

let wavesurfer;

const initialize = () => {
  console.log(audio)
  if (!audio) {
    error = true;
    loaded = true;
    return;
  }
  wavesurfer = WaveSurfer.create({
    container: `#waveform-${id}`,
    waveColor: colorsTable[color].wave,
    progressColor: colorsTable[color].progress,
    url: audio,
    barWidth: 2,
    height: height ?? 150
  });

  wavesurfer?.on('interaction', () => {
    playing = true;
  });

  wavesurfer?.on("ready", () => {
    loaded = true;
  });
}

onMount(() => {
  if (audio) initialize()
})

onDestroy(() => {
  wavesurfer?.destroy();
})

</script>

<div id={`waveform-${id}`} class:animate-pulse={!loaded} style:background-color={colorsTable[color].background}
class="relative w-full h-full min-h-[20px]" bind:offsetHeight={height}>
  {#if error}<div>An error occured</div>{/if}
  <button class="absolute left-1 bottom-1 z-10 p-1 cursor-pointer" style:background-color={colorsTable[color].background} on:click={() => playing = !playing}>
    {#if loaded && playing}
    <Square fill={colorsTable[color].button} strokeWidth=0 size=16/>
    {:else if loaded && !playing}
    <Play fill={colorsTable[color].button} strokeWidth=0 size=16/>
    {/if}
  </button>
</div>