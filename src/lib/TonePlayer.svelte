<script lang="ts">
  import { onMount, onDestroy } from 'svelte';
  import { Plus, Minus, Play, Square } from 'lucide-svelte';

  export let shape: "sine" | "triangle" | "square" | "sawtooth" = "triangle";
  $: if (oscillator) oscillator.type = shape;

  export let min = "G3";
  export let max = "E5";
  export let display: "full" | "compact" = "full";

  const notes = {
    'G3': 196.00,
    'G#3': 207.65,
    'A3': 220.00,
    'A#3': 233.08,
    'B3': 246.94,
    'C4': 261.63,
    'C#4': 277.18,
    'D4': 293.66,
    'D#4': 311.13,
    'E4': 329.63,
    'F4': 349.23,
    'F#4': 369.99,
    'G4': 392.00,
    'G#4': 415.30,
    'A4': 440.00,
    'A#4': 466.16,
    'B4': 493.88,
    'C5': 523.25,
    'C#5': 554.37,
    'D5': 587.33,
    'D#5': 622.25,
    'E5': 659.25
  };

  let notesArray = Object.keys(notes);
  $: minIndex = notesArray.findIndex(element => element == min);
  $: maxIndex = notesArray.findIndex(element => element == max);

  let oscillator;
  let gainNode;
  let audioCtx;

  let pitchIndex = 14;
  $: noteName = notesArray[pitchIndex];
  $: noteFrequency = notes[noteName];
  $: if (oscillator && audioCtx) oscillator.frequency.setValueAtTime(noteFrequency, audioCtx.currentTime);

  let isPlaying = false;
  $: if (audioCtx && gainNode) {
    if (isPlaying) {
      gainNode.gain.setTargetAtTime(1.0, audioCtx.currentTime, 0.1);
    } else {
      gainNode.gain.setTargetAtTime(0.0, audioCtx.currentTime, 0.1);
    }
  }

  onMount(() => {
    audioCtx = new (window.AudioContext || window.webkitAudioContext)();
    oscillator = audioCtx.createOscillator();
    gainNode = audioCtx.createGain();
    gainNode.gain.setValueAtTime(0.0, audioCtx.currentTime);
    oscillator.connect(gainNode);
    gainNode.connect(audioCtx.destination);
    oscillator.start();
  })

  onDestroy(() => {
    if (oscillator) {
      oscillator.stop();
      oscillator.disconnect();
    }
    if (gainNode) {
      gainNode.disconnect();
    }
    audioCtx.close();
  });

  function increasePitch() {
    if (pitchIndex === maxIndex || pitchIndex === notesArray.length - 1) return;
    pitchIndex++;
  }

  function decreasePitch() {
    if (pitchIndex === minIndex || pitchIndex === 0) return;
    pitchIndex--;
  }
</script>

<div class="grid h-full w-full bg-slate-600 container grid-rows-2 grid-cols-4">

  <!-- Play button -->
  <button class="col-start-1 col-end-3 row-start-1 row-end-3 text-white flex flex-col items-center justify-center hover:bg-slate-700" on:click={() => isPlaying = !isPlaying}>
    {#if isPlaying}
    <Square size=40/>
    {:else}
    <Play size=40/>
    {/if}
  </button>

  <!-- Note display -->
  <div class="col-start-3 col-end-5 row-start-1 row-end-2 flex flex-col justify-center items-center text-white bg-slate-800 text-xl">{noteName}</div>

  <!-- Buttons -->
  <button class="col-start-3 col-end-4 row-start-2 row-end-3 text-white flex flex-col items-center justify-center border-l-2 border-l-slate-700 hover:bg-slate-700" on:click={decreasePitch}><Minus/></button>
  <button class="col-start-4 col-end-5 row-start-2 row-end-3 text-white flex flex-col items-center justify-center border-l-2 border-l-slate-700 hover:bg-slate-700" on:click={increasePitch}><Plus/></button>
</div>

<style lang="postcss">
</style>
