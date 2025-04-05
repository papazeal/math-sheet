<script>
  import AnswerField from "./AnswerField.svelte";
  let operator = "+";
  let level = "easy";
  let mode = 'integer'
  let count = 26;
  $: problems = generateRandomMathProblems(count, operator, level);
  $: shuffle = Array.from({ length: count }, () => 2);

  function regenerateProblems() {
    if (level == "easy") {
      shuffle = Array.from({ length: count }, () => 2);
    } else {
      shuffle = Array.from({ length: count }, () =>
        Math.floor(Math.random() * 3)
      );
    }

    problems = generateRandomMathProblems(count, operator, level);
  }

  function getRandomNumber(min, max) {
    if(mode == 'integer'){
      return Math.floor(Math.random() * (max - min + 1)) + min;
    }
    if(mode == 'decimal'){
      
      let precision = 1;
      if (level == "easy") {
        precision = Math.random() < 0.5 ? 1 : 10; 
      } else if (level == "medium") {
        precision = Math.random() < 0.5 ? 1 : 10; 
      } else if (level == "hard") {
        precision = 10; 
      }
      return Number(((Math.floor(Math.random() * (max - min + 1)) + min)/precision).toFixed(2));
    }
    if(mode == 'fraction'){
      return Math.floor(Math.random() * (max - min + 1) * 2) / 2 + min;
    }
    
    // return Math.floor(Math.random() * (max - min + 1)) + min;
  }

  function writeNumber(num) {
    return num.toString().replace(/\B(?=(\d{3})+(?!\d))/g, ",");
    // if (mode == "integer") {
    //   return num.toString().replace(/\B(?=(\d{3})+(?!\d))/g, ",");
    // } else if (mode == "decimal") {
    //   num = level == "hard" ? num.toFixed(2) : num.toFixed(1);
    //   return num.toString().replace(/\B(?=(\d{3})+(?!\d))/g, ",");
    // } else if (mode == "fraction") {
    //   return num.toFixed(1).toString().replace(/\B(?=(\d{3})+(?!\d))/g, ",");
    // }
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
              a = getRandomNumber(1, 12);
              b = getRandomNumber(1, 12);
              break;
            case "hard":
              a = getRandomNumber(10, 99);
              b = getRandomNumber(10, 99);
              break;
          }
          result = Number((a + b).toFixed(4));
          break;
        case "-":
          switch (level) {
            case "easy":
              a = getRandomNumber(1, 9);
              b = getRandomNumber(1, 9);
              if (a < b) {
                [a, b] = [b, a];
              }
              break;
            case "medium":
              a = getRandomNumber(1, 12);
              b = getRandomNumber(1, 12);
              break;
            case "hard":
              a = getRandomNumber(10, 99);
              b = getRandomNumber(10, 99);
              break;
          }
          result = Number((a - b).toFixed(4));
          break;
        case "×":
          switch (level) {
            case "easy":
              a = getRandomNumber(1, 9);
              b = getRandomNumber(1, 9);
              break;
            case "medium":
              a = getRandomNumber(1, 12);
              b = getRandomNumber(2, 12);
              break;
            case "hard":
              a = getRandomNumber(10, 99);
              b = getRandomNumber(10, 99);
              break;
          }
          result = Number((a * b).toFixed(4));
          break;
        case "÷":
          switch (level) {
            case "easy":
              a = getRandomNumber(1, 9);
              b = getRandomNumber(1, 4);
              break;
            case "medium":
              a = getRandomNumber(1, 12);
              b = getRandomNumber(2, 12);
              break;
            case "hard":
              a = getRandomNumber(10, 99);
              b = getRandomNumber(10, 99);
              break;
          }
          //   result = a * b;
          result = a;
          a = Number((a * b).toFixed(4))
          // result = a / b;
          break;
      }
      let key = `${a}-${b}-${operator}`;
      if (!usedPairs.has(key)) {
        usedPairs.add(key);
        problems.push({ a, b, operator, result, key });
      }
    }

    return problems;
  }
</script>

<div>
  <div
    class=" mb-14 print:mb-12  gap-4 grid  lg:flex lg:gap-x-6   text-lg print:flex print:flex-wrap print:text-base "
  >
  
    <div >
      <div class="flex  rounded overflow-hidden  justify-center">
        {#each ["+", "-", "×", "÷"] as i}
          <button
            class="py-0.5 px-3 text-center border-r bg-gray-200 border-gray-300 last:border-0  cursor-pointer hover:bg-gray-300 first:rounded-l last:rounded-r"
            class:!bg-gray-700={operator == i}
            class:text-white={operator == i}
            on:click={() => {
              operator = i;
              regenerateProblems();
            }}
          >
            {i}
          </button>
        {/each}
      </div>
    </div>
    <div>
      <div class="flex rounded  overflow-hidden justify-center">
        {#each ["easy", "medium", "hard"] as i}
          <button
            class="py-0.5 px-3 bg-gray-200 text-center border-r border-gray-300 last:border-0 bg-gray-200 cursor-pointer hover:bg-gray-300 capitalize first:rounded-l last:rounded-r"
            class:!bg-gray-700={level == i}
            class:text-white={level == i}
            on:click={() => {
              level = i;
              regenerateProblems();
            }}
          >
            {i}
          </button>
        {/each}
      </div>
    </div>
    <div>
      <div class="flex rounded overflow-hidden   justify-center">
        {#each [{label:"1", value:"integer"}, {label:"0.1", value:"decimal"}] as i}
          <button
            class="py-0.5 px-3 text-center border-r bg-gray-200 border-gray-300 last:border-0 cursor-pointer hover:bg-gray-300 capitalize first:rounded-l last:rounded-r"
            class:!bg-gray-700={mode == i.value}
            class:text-white={mode == i.value}
            on:click={() => {
              mode = i.value;
              regenerateProblems();
            }}
          >
            {i.label}
          </button>
        {/each}
      </div>
    </div>
  
    <div class=" flex justify-center print:hidden mt-4 lg:mt-0">
      <button
        class="bg-blue-400 text-white px-4 py-1 rounded cursor-pointer flex items-center"
        on:click={() => window.print()}
      ><svg class="w-6 mr-2" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24"><g fill="none" stroke="currentColor" stroke-linecap="round" stroke-linejoin="round" stroke-width="1.5"><path d="M19 10V5a1 1 0 0 0-1-1H6a1 1 0 0 0-1 1v5m15 0H4a1 1 0 0 0-1 1v8a1 1 0 0 0 1 1h16a1 1 0 0 0 1-1v-8a1 1 0 0 0-1-1"/><path d="M17.5 20v-3a1 1 0 0 0-1-1H11a1 1 0 0 0-1 1v3m-4-7h2"/></g></svg> Print
      </button>
    </div>
  </div>

  <div class="text-2xl grid sm:grid-cols-2 gap-9 gap-x-14">
    {#each problems as p, index}
      <div class=" flex gap-4">
        {#if shuffle[index] == 0}
        {#key p.key}
        <AnswerField answer={p.a} />{/key}
        {:else}
          <div>
            {writeNumber(p.a)}
          </div>
        {/if}

        <div>{p.operator}</div>

        {#if shuffle[index] == 1}
        {#key p.key}
        <AnswerField answer={p.b} />{/key}
        {:else}
          <div>
            {writeNumber(p.b)}
          </div>
        {/if}

        <div>=</div>
        <div class="hidden">{p.result}</div>
        {#if shuffle[index] == 2}
        {#key p.key}
        <AnswerField answer={p.result} />{/key}
        {:else}
          <div>
            {writeNumber(p.result)}
          </div>
        {/if}
      </div>
    {/each}
  </div>
</div>
