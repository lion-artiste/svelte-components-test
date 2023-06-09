<script lang="ts">
  import { createEventDispatcher } from "svelte";
  import Interactable from "./Interactable.svelte";

  let dispatch = createEventDispatcher();

  const next = () => dispatch("next");
  const prev = () => dispatch("prev");

  export let pos: "left" | "right" | "center" = "center";
  $: left = (pos == "center") ? "0px" : (pos == "left") ? "-100%" : "100%";
  export let bgColor = "transparent";

  function getColorBrightness(color) {
    const numericColor = parseInt(color.substr(1), 16);

    const red = (numericColor >> 16) & 255;
    const green = (numericColor >> 8) & 255;
    const blue = numericColor & 255;

    const brightness = (red * 299 + green * 587 + blue * 114) / 1000;

    return brightness;
  }

  $: textColor = (bgColor !== "transparent") ? (getColorBrightness(bgColor) >= 127) ? "black" : "white" : "black";
</script>

<div class="w-full h-full absolute top-0 left-0 transition-left flex flex-col items-center justify-center gap-x-3 shadow-md" style:left style:color={textColor}>

  <Interactable detectClickPos=true {bgColor} class="absolute h-full w-full top-0 left-0" on:clickRight={next} on:clickLeft={prev} on:swipeRight={prev} on:swipeLeft={next}></Interactable> <!-- Right panel -->

  <div class="z-[1] flex flex-col items-center justify-center gap-y-3 p-2 cursor-default"> <!-- Main panel -->
    <slot/>
  </div>
</div>

<style>
.transition-left {
  transition: left 0.3s ease-out;
}
</style>