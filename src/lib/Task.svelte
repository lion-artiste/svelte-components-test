<script lang="ts">
interface TASK {
  name: string,

}

export let task;
export let state = "close";
</script>

<div class="bg-red-500 relative h-[80px]">
  <div class="bg-white absolute t-0 left-0 w-full h-full border-2">
    <div class="">{task?.name}</div>
  </div>
</div>