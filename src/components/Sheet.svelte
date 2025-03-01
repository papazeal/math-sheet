<script>
  let operator = "+";
  let level = "easy";
  let count = 28;
  $: problems = generateRandomMathProblems(count, operator, level);

  function getRandomNumber(min, max) {
    return Math.floor(Math.random() * (max - min + 1)) + min;
  }

  function generateRandomMathProblems(count, operator, level) {
    const problems = [];

    for (let i = 0; i < count; i++) {
      let a, b, result;

      switch (operator) {
        case "+":
          switch (level) {
            case "easy":
              a = getRandomNumber(1, 9);
              b = getRandomNumber(1, 9);
              break;
            case "medium":
              a = getRandomNumber(10, 99);
              b = getRandomNumber(10, 99);
              break;
            case "hard":
              a = getRandomNumber(100, 999);
              b = getRandomNumber(100, 999);
              break;
          }
          result = a + b;
          break;
        case "-":
          switch (level) {
            case "easy":
              a = getRandomNumber(1, 9);
              b = getRandomNumber(1, 9);
              break;
            case "medium":
              a = getRandomNumber(10, 99);
              b = getRandomNumber(10, 99);
              break;
            case "hard":
              a = getRandomNumber(100, 999);
              b = getRandomNumber(100, 999);
              break;
          }
          result = a - b;
          break;
        case "×":
          switch (level) {
            case "easy":
              a = getRandomNumber(1, 9);
              b = getRandomNumber(1, 9);
              break;
            case "medium":
              a = getRandomNumber(10, 99);
              b = getRandomNumber(1, 9);
              break;
            case "hard":
              a = getRandomNumber(100, 999);
              b = getRandomNumber(10, 99);
              break;
          }
          result = a * b;
          break;
        case "÷":
          switch (level) {
            case "easy":
              a = getRandomNumber(2, 9);
              b = getRandomNumber(2, 9);
              break;
            case "medium":
              a = getRandomNumber(10, 99);
              b = getRandomNumber(2, 9);
              break;
            case "hard":
              a = getRandomNumber(10, 99);
              b = getRandomNumber(10, 99);
              break;
          }
          //   result = a * b;
          a = a * b;
          result = a / b;
          break;
      }

      problems.push({ a, b, operator, result });
    }

    return problems;
  }
</script>

<div>
  <div class="print:hidden mb-14 grid sm:flex gap-5 sm:gap-12 text-xl">
    <div>
      <div class="flex gap-3">
        {#each ["+", "-", "×", "÷"] as i}
          <button
            class="py-0.5 px-2 w-8 text-center rounded bg-gray-100 cursor-pointer hover:bg-gray-200"
            class:!bg-gray-700={operator == i}
            class:text-white={operator == i}
            on:click={() => (operator = i)}
          >
            {i}
          </button>
        {/each}
      </div>
    </div>
    <div>
      <div class="flex gap-3">
        {#each ["easy", "medium", "hard"] as i}
          <button
            class="py-0.5 px-2 text-center rounded bg-gray-100 cursor-pointer hover:bg-gray-200 capitalize"
            class:!bg-gray-700={level == i}
            class:text-white={level == i}
            on:click={() => (level = i)}
          >
            {i}
          </button>
        {/each}
      </div>
    </div>
    <div class="sm:ml-auto">
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
