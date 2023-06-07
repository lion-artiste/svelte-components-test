<script lang="ts">
  import { onMount } from 'svelte';
  import Vex from 'vexflow';

  const { Renderer, Stave, Voice, TickContext, StaveNote, Formatter, Accidental } = Vex.Flow;

  export let notes = [];
  export let height: number = 120;
  export let type: "melody" | "rhythm" | "pitch" = "melody";
  export let debug: boolean = true;
  export let key: "C" | "C#" | "D" | "Eb" | "E" | "F" | "F#" | "G" | "Ab" | "A" | "Bb" | "B" = "C";

  let renderer;
  let container: HTMLElement;
  let stave;
  let width;
  let parentWidth;
  let elWidth;
  $: sheetWidth = parentWidth;

  $: if (container && sheetWidth) {renderNotes();}

  function renderNotes() {

    container.innerHTML = "";
    console.log("InnerHTML deleted");

    renderer = new Renderer(
      container,
      Renderer.Backends.SVG
    );

    renderer.resize(sheetWidth, height);

    const context = renderer.getContext();

    context.setFont('Arial', 10);

    stave = new Stave(10, 0, sheetWidth - 30);
    stave.addClef('treble').addTimeSignature('4/4');
    stave.setContext(context).draw();

    const staveNotesArray = notes.map((note) => {
      return new StaveNote({ keys: [note.key], duration: note.duration });
    });

    const voice = new Voice({ num_beats: 4, beat_value: 4 });
    voice.addTickables(staveNotesArray);
    new Formatter().joinVoices([voice]).format([voice], 350);
    voice.draw(context, stave);
  }
</script>

<div class="w-full overflow-x-scroll border-sky-500" class:border-2={debug} style:height={`${height}px`} bind:clientWidth={parentWidth}>
  <div class="max-h-full maw-w-full border-red-500" class:border-2={debug} bind:this={container} bind:clientWidth={elWidth}></div>
</div>

{#if debug}<div><span class="text-sky-500">{parentWidth}</span> <span class="text-red-500">{elWidth}</span></div>{/if}