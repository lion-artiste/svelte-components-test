<script lang="ts">
interface TASK {
  name: string,

}

export let task;
export let morePanel = false;
</script>

<div class="relative h-[80px] flex flex-row">

  <!-- Backgrounds -->
  <div class="w-full bg-yellow-500"></div>
  <div class="w-full bg-red-500"></div>

  <!-- Main task -->
  <div class="bg-white absolute t-0 left-10 w-full h-full border-2">
    <div class="">{task?.name}</div>
  </div>
</div>
<div></div>