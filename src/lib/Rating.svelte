<script lang="ts">
  import { createEventDispatcher} from "svelte";

  let dispatch = createEventDispatcher();

  export let total: number = 10;
  export let grade: number = 5;
  export let size: "xs" | "sm" | "md" | "lg" = "md";
  export let shape: "heart" | "star" = "heart";
  $: dispatch("change", grade);

</script>

<div class="rating" class:rating-xs={size=="xs"} class:rating-sm={size=="sm"} class:rating-md={size=="md"} class:rating-lg={size=="lg"} class:gap-1={shape=="heart"}>
{#each Array(total) as _, index}
  <input type="radio" bind:group={grade} value={index+1} name="grade" class="mask bg-red-400" class:mask-heart={shape=="heart"} class:mask-star-2={shape=="star"} class:bg-red-400={shape=="heart"} class:bg-yellow-400={shape=="star"}/>
{/each}
</div>