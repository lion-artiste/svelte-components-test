<script lang="ts">
  import Task from "./Task.svelte";

  interface TASK {
    name: string,
    date: Date
  }

  export let tasks: TASK[] = [];
  export let showClosed = true;

  function completeTask(task: TASK) {
    tasks.find((t,i) => {
      if (t.id == task.id) {
        tasks[i].open = false;
        tasks[i].dateDone = new Date();
        return true;
      }
    });
  }
</script>

<div class="flex flex-col items-strech gap-y-3">

  <div class="flex flex-col items-stretch gap-y-1">
    {#each tasks.filter(t => t.open) as task (task.id)}
    <Task task={task} on:deleted on:completed = {(event) => {completeTask(event.detail.task)}}/>
    {/each}
  </div>

  {#if showClosed}
  <div class="flex flex-col items-stretch gap-y-1">
    {#each tasks.filter(t => !t.open).sort((a,b) => b.dateDone - a.dateDone) as task (task.id)}
    <Task task={task} on:deleted on:reported/>
    {/each}
  </div>
  {/if}

</div>