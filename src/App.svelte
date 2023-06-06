<script lang="ts">
  import Waveform from './lib/Waveform.svelte'
  import Task from './lib/Task.svelte'
  import MusicSheet from './lib/MusicSheet.svelte';
  import { flip } from 'svelte/animate';

  let tasks = [{
      name: "Sortir la poubelle",
      open: true,
      id: 1
    },{
      name: "Laver le chien",
      open: true,
      id: 2
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
    <h1>Waveform</h1>
    <div>
      <Waveform color="red"/>
    </div>
  </div>

  <div>
    <h1>Task</h1>
    <div class="flex flex-col gap-y-1">
    {#each tasks.filter(t => t.open) as task (task.id)}
    <Task task={task} on:deleted={(event) => mark(event.detail.task, false)}/>
    {/each}
    </div>
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
