<script>
  let name = "world";
  let count = 0;
  let operator = "+";
  let level = "easy";
  $: problems = generateRandomMathProblems(28, operator, level);

  function generateRandomMathProblems(count, operator, level) {
    const operators = ["+", "-", "×"];
    const problems = [];
    let max = 10;
    let min = 1;
    switch (level) {
      case "easy":
        min = 1;
        max = 10;
        break;
      case "medium":
        min = 10;
        max = 99;
        break;
      case "hard":
        min = 100;
        max = 999;
        break;
    }

    for (let i = 0; i < count; i++) {
      let a = Math.floor(Math.random() * max) + min;
      let b = Math.floor(Math.random() * max) + min;
      //   let operator = operators[Math.floor(Math.random() * operators.length)];
      let result;

      switch (operator) {
        case "+":
          result = a + b;
          break;
        case "-":
          result = a - b;
          break;
        case "×":
          result = a * b;
          break;
        case "÷":
          result = a * b;
          a = result;
          result = a;
          break;
      }

      problems.push({ a, b, operator, result });
    }

    return problems;
  }
</script>

<div>
  <div class="print:hidden mb-14 flex gap-12">
    <div>
      <div class="flex gap-2 text-xl">
        {#each ["+", "-", "×", "÷"] as i}
          <button
            class="py-0.5 px-2 w-8 text-center rounded bg-gray-100 cursor-pointer hover:bg-gray-200"
            class:bg-gray-700!={operator == i}
            class:text-white={operator == i}
            on:click={(operator = i)}
          >
            {i}
          </button>
        {/each}
      </div>
    </div>
    <div>
      <div class="flex gap-2 text-xl">
        {#each ["easy", "medium", "hard"] as i}
          <button
            class="py-0.5 px-2 text-center rounded bg-gray-100 cursor-pointer hover:bg-gray-200"
            class:bg-gray-700!={level == i}
            class:text-white={level == i}
            on:click={(level = i)}
          >
            {i}
          </button>
        {/each}
      </div>
    </div>
    <div class="ml-auto">
      <button
        class="bg-blue-400 text-white px-4 py-1 rounded cursor-pointer"
        on:click={() => window.print()}
      >
        Print
      </button>
    </div>
  </div>

  <div class="text-2xl grid sm:grid-cols-2 gap-11">
    {#each problems as p}
      <div class=" flex gap-4">
        <div>{p.a.toString().replace(/\B(?=(\d{3})+(?!\d))/g, ",")}</div>
        <div>{p.operator}</div>
        <div>{p.b.toString().replace(/\B(?=(\d{3})+(?!\d))/g, ",")}</div>
        <div>=</div>
        <div class="hidden">{p.result}</div>
        <div class="border-b border-gray-500 w-full"></div>
      </div>
    {/each}
  </div>
</div>
