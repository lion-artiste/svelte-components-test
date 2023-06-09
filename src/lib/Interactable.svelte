<script>
  import { createEventDispatcher } from 'svelte';

  export let detectClickPos = false;

  let startX = null;
  let startY = null;
  let dispatch = createEventDispatcher();
  let el;
  export let bgColor = "transparent";

  function handleStart(pos) {
      startX = pos.clientX;
      startY = pos.clientY;
  }

  function handleEnd(pos) {
    if (!startX || !startY) return;

    const deltaX = pos.clientX - startX;
    const deltaY = pos.clientY - startY;
    const distance = Math.sqrt(deltaX * deltaX + deltaY * deltaY);

    if (distance < 10) { // Click detected

      dispatch("click");

      if (detectClickPos) {

        let clickRight = pos.clientX > el.offsetLeft + el.offsetWidth/2;
        let clickUp = pos.clientY < el.offsetTop + el.offsetHeight/2;

        if (clickRight && clickUp) { // Click on the right
          dispatch("clickRight");
          dispatch("clickUp");
          dispatch("clickRightUp");
        } else if (clickRight && !clickUp) {
          dispatch("clickRight");
          dispatch("clickDown");
          dispatch("clickRightDown");
        } else if (!clickRight && clickUp) {
          dispatch("clickLeft");
          dispatch("clickUp");
          dispatch("clickLeftUp");
        } else {
          dispatch("clickLeft");
          dispatch("clickDown");
          dispatch("clickLeftDown");
        }
      }
      return;
    }

    if (Math.abs(deltaX) > Math.abs(deltaY)) {
      // Horizontal swipe detected
      if (deltaX > 0) {
        dispatch('swipeRight');
      } else {
        dispatch('swipeLeft');
      }
    } else {
      if (deltaY > 0) {
        dispatch('swipeUp');
      } else {
        dispatch('swipeDown');
      }
    }

    startX = null;
    startY = null;
  }
</script>

<div
  on:click|preventDefault={() => {}}
  on:touchstart={(event) => handleStart(event.touches[0])}
  on:mousedown={(event) => handleStart(event)}
  on:touchend={(event) => handleEnd(event.changedTouches[0])}
  on:mouseup={(event) => handleEnd(event)}
  class={`cursor-pointer ${$$props.class}`}
  bind:this={el}
  style:background-color={bgColor}
>
  <slot />
</div>

<style>
  div {
    user-select: none;
    cursor: pointer;
  }
</style>
