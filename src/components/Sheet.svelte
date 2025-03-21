<script>
  let operator = "+";
  let level = "easy";
  let count = 26;
  $: problems = generateRandomMathProblems(count, operator, level);
  $: shuffle = Array.from({ length: count }, () =>
    Math.floor(Math.random() * 3)
  );

  function regenerateProblems() {
    shuffle = Array.from({ length: count }, () =>
      Math.floor(Math.random() * 3)
    );
    problems = generateRandomMathProblems(count, operator, level);
  }

  function getRandomNumber(min, max) {
    return Math.floor(Math.random() * (max - min + 1)) + min;
  }

  function generateRandomMathProblems(count, operator, level) {
    const problems = [];
    const usedPairs = new Set();

    while (problems.length < count) {
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
              b = getRandomNumber(2, 12);
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
              a = getRandomNumber(1, 9);
              b = getRandomNumber(1, 9);
              break;
            case "medium":
              a = getRandomNumber(10, 99);
              b = getRandomNumber(2, 12);
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
      let key = `${a}-${b}-${operator}`;
      if (!usedPairs.has(key)) {
        usedPairs.add(key);
        problems.push({ a, b, operator, result });
      }
    }

    return problems;
  }
</script>

<div>
  <div
    class=" mb-14 print:mb-12 grid sm:flex sm:flex-wrap gap-5 sm:gap-12 text-xl"
  >
    <div>
      <div class="flex gap-4 sm:gap-3 justify-center">
        {#each ["+", "-", "×", "÷"] as i}
          <button
            class="py-0.5 w-8 text-center rounded bg-gray-200 cursor-pointer hover:bg-gray-300"
            class:!bg-gray-700={operator == i}
            class:text-white={operator == i}
            on:click={() => {
              regenerateProblems();
              operator = i;
            }}
          >
            {i}
          </button>
        {/each}
      </div>
    </div>
    <div>
      <div class="flex gap-4 sm:gap-3 justify-center">
        {#each ["easy", "medium", "hard"] as i}
          <button
            class="py-0.5 px-2.5 text-center rounded bg-gray-200 cursor-pointer hover:bg-gray-300 capitalize"
            class:!bg-gray-700={level == i}
            class:text-white={level == i}
            on:click={() => {
              regenerateProblems();
              level = i;
            }}
          >
            {i}
          </button>
        {/each}
      </div>
    </div>
    <div class="sm:ml-auto flex justify-center print:hidden">
      <button
        class="bg-blue-400 text-white px-6 py-1 rounded cursor-pointer"
        on:click={() => window.print()}
      >
        Print
      </button>
    </div>
  </div>

  <div class="text-2xl grid sm:grid-cols-2 gap-11 gap-x-20">
    {#each problems as p, index}
      <div class=" flex gap-4">
        {#if shuffle[index] == 0}
          <div class="border border-gray-500 w-full rounded"></div>
        {:else}
          <div>
            {p.a.toString().replace(/\B(?=(\d{3})+(?!\d))/g, ",")}
          </div>
        {/if}

        <div>{p.operator}</div>

        {#if shuffle[index] == 1}
          <div class="border border-gray-500 w-full rounded"></div>
        {:else}
          <div>
            {p.b.toString().replace(/\B(?=(\d{3})+(?!\d))/g, ",")}
          </div>
        {/if}

        <div>=</div>
        <div class="hidden">{p.result}</div>
        {#if shuffle[index] == 2}
          <div class="border border-gray-500 w-full rounded"></div>
        {:else}
          <div>
            {p.result.toString().replace(/\B(?=(\d{3})+(?!\d))/g, ",")}
          </div>
        {/if}
      </div>
    {/each}
  </div>
</div>
