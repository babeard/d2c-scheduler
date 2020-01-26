<script>
  import { draw } from "svelte/transition";
  
  export let viewBox = "0 0 24 24";
  export let size = "24px";
  export let width = size;
  export let height = size;
  export let stroke = "currentColor";
  export let fill = "none";
  export let strokeLineCap = "butt";
  export let strokeLineJoin = "round";
  export let strokeWidth = "2";
 
  /* animations */
  export let duration = 250;
  export let speed = 1;
  export let delay = 0;
</script>

<svg
  xmlns="http://www.w3.org/2000/svg"
  {width}
  {height}
  {stroke}
  {fill}
  {viewBox}
  stroke-width={strokeWidth}
  stroke-linecap={strokeLineCap}
  stroke-linejoin={strokeLineJoin}
  class="quote! {$$props.class}">
  <polyline
    points="4 12 9 17 20 6"
    in:draw={{ duration, speed, delay }} />
</svg>
