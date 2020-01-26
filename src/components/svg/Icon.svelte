<script>
  export let data;
  export let viewBox = "0 0 24 24";
  export let size = "24px";
  export let width = size;
  export let height = size;
  export let stroke = "currentColor";
  export let fill = "none";
  export let strokeLineCap = "round";
  export let strokeLineJoin = "round";
  export let strokeWidth = "2";
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
  {@html data}
</svg>