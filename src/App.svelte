<script lang="ts">
  import Waveform from './lib/Waveform.svelte';
  import TaskList from './lib/TaskList.svelte';
  import MusicSheet from './lib/MusicSheet.svelte';
  import TonePlayer from "./lib/TonePlayer.svelte";
  import Steps from './lib/Steps.svelte';
  import Metronome from './lib/Metronome.svelte';
  import Rating from "./lib/Rating.svelte";
  import { flip } from 'svelte/animate';

  let tasks = [{
      name: "Sortir la poubelle",
      open: true,
      id: 1,
      date: new Date(),
      dateDone: null
    },{
      name: "Laver le chien",
      open: true,
      id: 2,
      date: new Date(),
      dateDone: null
    },{
      name: "Créer component Waveform",
      open: false,
      id: 3,
      date: new Date(),
      dateDone: new Date()
    }
  ];

  const notes = [
    { key: 'C/4', duration: 'q' },
    { key: 'D/4', duration: 'q' },
    { key: 'E/4', duration: 'q' },
    { key: 'F/4', duration: 'q' },
  ];

  function deleteTask(task) {
    tasks = tasks.filter(t => t !== task);
  }

	function mark(task, open) {
		task.open = open;
		deleteTask(task);
		tasks = tasks.concat(task);
	}
</script>

<main class="flex flex-col gap-y-10 items-stretch">
  <div>
    <h1>Slides</h1>
    <div class="h-[300px]">
    <Steps>

        <svelte:fragment slot="step1">
          <div>Choisissez votre date de naissance :</div>
          <Rating on:change={(event) => console.log(event.detail)}/>
        </svelte:fragment>

        <svelte:fragment slot="step2">
          <input class="input input-sm text-black" type="text" id="name" name="name" required minlength="4" maxlength="8" size="10">
          <input type="date" class="input input-sm text-black">
        </svelte:fragment>
    </Steps>
    </div>
  </div>
  <div>
    <h1>Waveform</h1>
    <div class="flex flex-col items-stretch gap-y-3">
    <div class="h-[300px]">
      <Waveform id=1 audio="https://webaudioapi.com/samples/metering/sounds/chrono.mp3" on:play={(event) => console.log(event.detail)} on:stop={(event) => console.log(event.detail)}/>
    </div>
      <Waveform id=2/>
      <div class="h-[50px]">
      <Waveform id=3 audio="https://webaudioapi.com/samples/metering/sounds/chrono.mp3"/>
      </div>
    </div>
  </div>

  <div>
    <h1>Tasks</h1>
    <TaskList tasks={tasks} on:deleted={(event) => console.log(event.detail)}/>
  </div>

    <h1>Tone Player</h1>
  <div class="h-[100px] w-[200px] self-center">
    <TonePlayer/>
  </div>

  <h1>Metronome</h1>
  <div class="h-[200px] w-[300px] self-center">
    <Metronome/>
  </div>

  <div>
    <h1>Music Sheet</h1>
    <div class="border-2">
      <MusicSheet {notes}/>
    </div>
  </div>
</main>

<style>
</style>
