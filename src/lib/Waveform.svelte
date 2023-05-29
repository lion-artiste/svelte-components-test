<script lang="ts">
import WaveSurfer from 'wavesurfer.js';
import { onMount } from "svelte";
import songFile from "/song_test.mp3"

export let baseColor = "#4F4A85";
export let progressColor = "#383351";

let wavesurfer;

onMount(() => {
  wavesurfer = WaveSurfer.create({
    container: "#waveform",
    waveColor: baseColor,
    progressColor,
    url: songFile,
    barWidth: 2
})


wavesurfer.on('interaction', () => {
  wavesurfer.play()
})

})

</script>

<div id="waveform"></div>