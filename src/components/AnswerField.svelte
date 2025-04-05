<script>
  let { answer } = $props();
  let value = $state();
</script>

<input
  type="number"
  bind:value
  class="border border-gray-500 w-full rounded text-center outline-0 [appearance:textfield] [&::-webkit-outer-spin-button]:appearance-none [&::-webkit-inner-spin-button]:appearance-none focus:bg-yellow-50 py-0.5"
  class:!bg-green-100={value === answer}
/>
