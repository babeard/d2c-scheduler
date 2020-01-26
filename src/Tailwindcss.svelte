<style global>
  @tailwind base;
  @tailwind components;
  @tailwind utilities;

  body {
    margin: 0px;
    padding: 0px;
    @apply font-sans;
  }

  button {
    @apply transition-all;
    @apply duration-200;
    @apply ease-out;
  }

  button:active {
    transform: scale(0.9);
    @apply bg-blue-300;
    @apply text-white;
  }

  button.valid:active {
    @apply bg-blue-500;
    @apply text-white;
  }
</style>
