<script lang="ts">
  import Waveform from './lib/Waveform.svelte'
  import Task from './lib/Task.svelte'

  let task = {
    name: "Sortir la poubelle",
    state: "started",
  }
</script>

<main>
  <div>
    <h1>Waveform</h1>
    <Waveform/>
  </div>

  <h1>Task</h1>
  <Task task={task}/>
</main>

<style>
</style>
