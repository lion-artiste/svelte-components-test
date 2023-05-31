<script lang="ts">
import WaveSurfer from 'wavesurfer.js';
import { onMount } from "svelte";
import songFile from "/song_test.mp3"

let COLORS = ["blue", "red"];
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
  }
}

let loaded = false;

function getRandomColor() {
  return COLORS[Math.floor(Math.random()*COLORS.length)];
}

export let playing = false;
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
    wavesurfer.play()
  });

  wavesurfer.on("ready", () => {
    loaded = true;
  });

})

</script>

<div class:animate-pulse={!loaded} id="waveform" style:background-color={colorsTable[color].background}></div>