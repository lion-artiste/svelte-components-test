<script lang="ts">
import WaveSurfer from 'wavesurfer.js';
import { onMount } from "svelte";

let htmlElement;
let wavesurfer;

onMount(() => {
  wavesurfer = WaveSurfer.create({
    container: "#waveform",
    waveColor: '#4F4A85',
    progressColor: '#383351',
    url: '/audio.mp3',
})

})

</script>

<div id="waveform" bind:this={htmlElement}></div>