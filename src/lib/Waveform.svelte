<script lang="ts">
import WaveSurfer from 'wavesurfer.js';
import { onMount } from "svelte";

let COLORS = ["blue", "red", "yellow", "green"];
let colorsTable = {
  "blue": {
    wave: "hsla(213, 76%, 55%, 1)",
    progress: "hsla(213, 75%, 40%, 1)",
    background: "hsla(213, 76%, 90%, 1)"
  },
  "red": {
    wave: "hsla(0, 76%, 55%, 1)",
    progress: "hsla(0, 75%, 40%, 1)",
    background: "hsla(0, 76%, 90%, 1)"
  },
  "yellow": {
    wave: "hsla(48, 76%, 55%, 1)",
    progress: "hsla(48, 75%, 40%, 1)",
    background: "hsla(48, 76%, 90%, 1)"
  },
  "green": {
    wave: "hsla(112, 76%, 55%, 1)",
    progress: "hsla(112, 75%, 40%, 1)",
    background: "hsla(112, 76%, 90%, 1)"
  }
}

let loaded = false;

function getRandomColor() {
  return COLORS[Math.floor(Math.random()*COLORS.length)];
}

export let playing = true;
export let color: typeof COLORS[number] = getRandomColor();
export let audio = "https://webaudioapi.com/samples/metering/sounds/chrono.mp3";

let wavesurfer;

onMount(() => {
  wavesurfer = WaveSurfer.create({
    container: "#waveform",
    waveColor: colorsTable[color].wave,
    progressColor: colorsTable[color].progress,
    url: audio,
    barWidth: 2
  });


  wavesurfer.on('interaction', () => {
    wavesurfer.play();
  });

  wavesurfer.on("ready", () => {
    loaded = true;
  });

  if (playing) wavesurfer.play();

})

</script>

<div class:animate-pulse={!loaded} id="waveform" style:background-color={colorsTable[color].background}></div>