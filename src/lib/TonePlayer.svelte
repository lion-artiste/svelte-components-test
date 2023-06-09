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

<div>
  <div class="flex flex-col items-center justify-center gap-y-2">

    <div class="join">
      <button on:click={decreasePitch} class="btn btn-primary join-item text-white"><Minus size=16/></button>
      <div on:click={() => isPlaying = !isPlaying} class="btn btn join-item w-[80px] text-lg">{noteName}</div>
      <button on:click={increasePitch} class="btn btn-primary join-item text-white" ><Plus size=16/></button>
    </div>

    {#if display !== "compact"}
    <button class="btn btn-circle btn-primary" on:click={() => isPlaying = !isPlaying}>
      {#if isPlaying}<Square/>{:else}<Play/>{/if}
    </button>
    {/if}
  </div>

  <div>
  </div>
</div>
