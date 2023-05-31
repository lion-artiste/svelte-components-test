<script lang="ts">
  import Waveform from './lib/Waveform.svelte'
  import Task from './lib/Task.svelte'
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

  function deleteTask(task) {
    tasks = tasks.filter(t => t !== task);
  }

	function mark(task, open) {
		task.open = open;
		deleteTask(task);
		tasks = tasks.concat(task);
	}
</script>

<main>
  <div>
    <h1>Waveform</h1>
    <Waveform color="red"/>
  </div>

  <h1>Task</h1>
  <div class="flex flex-col gap-y-1">
  {#each tasks.filter(t => t.open) as task (task.id)}
  <Task task={task} on:deleted={(event) => mark(event.detail.task, false)}/>
  {/each}
  </div>
</main>

<style>
</style>
