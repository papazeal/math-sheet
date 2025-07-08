<script>
  import AnswerField from "./AnswerField.svelte";
  import FractionDisplay from "./FractionDisplay.svelte";
  let operator = "+";
  let level = "easy";
  let mode = "integer";
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

    if (mode == "fraction") {
      count = 18;
    } else {
      count = 26;
    }

    problems = generateRandomMathProblems(count, operator, level);
  }

  function getRandomNumber(min, max) {
    if (mode == "integer") {
      return Math.floor(Math.random() * (max - min + 1)) + min;
    }
    if (mode == "decimal") {
      let precision = 1;
      if (level == "easy") {
        precision = Math.random() < 0.5 ? 1 : 10;
      } else if (level == "medium") {
        precision = Math.random() < 0.5 ? 1 : 10;
      } else if (level == "hard") {
        precision = 10;
      }
      return Number(
        (
          (Math.floor(Math.random() * (max - min + 1)) + min) /
          precision
        ).toFixed(2)
      );
    }
    if (mode == "fraction") {
      // Generate a random positive proper fraction
      const denominator = Math.floor(Math.random() * 8) + 2; // 2 to 9
      const numerator = Math.floor(Math.random() * (denominator - 1)) + 1; // 1 to denominator-1
      return { numerator, denominator };
    }

    // return Math.floor(Math.random() * (max - min + 1)) + min;
  }

  function writeNumber(num) {
    // Handle fraction objects
    if (
      mode === "fraction" &&
      typeof num === "object" &&
      num !== null &&
      "numerator" in num &&
      "denominator" in num
    ) {
      return num.numerator + "/" + num.denominator;
    }
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

  function toImproperFraction(frac) {
    return {
      numerator: frac.numerator,
      denominator: frac.denominator,
    };
  }

  function fromImproperFraction(numerator, denominator) {
    // Always return a proper fraction (numerator < denominator)
    numerator = numerator % denominator;
    if (numerator < 0) numerator += denominator; // ensure positive
    return {
      numerator,
      denominator,
    };
  }

  function gcd(a, b) {
    return b === 0 ? a : gcd(b, a % b);
  }

  function reduceFraction(frac) {
    const improper = toImproperFraction(frac);
    const divisor = gcd(Math.abs(improper.numerator), improper.denominator);
    return fromImproperFraction(
      Math.floor(improper.numerator / divisor),
      Math.floor(improper.denominator / divisor)
    );
  }

  function addFractions(a, b) {
    const fa = toImproperFraction(a);
    const fb = toImproperFraction(b);
    const numerator =
      fa.numerator * fb.denominator + fb.numerator * fa.denominator;
    const denominator = fa.denominator * fb.denominator;
    return reduceFraction(fromImproperFraction(numerator, denominator));
  }

  function subtractFractions(a, b) {
    const fa = toImproperFraction(a);
    const fb = toImproperFraction(b);
    const numerator =
      fa.numerator * fb.denominator - fb.numerator * fa.denominator;
    const denominator = fa.denominator * fb.denominator;
    return reduceFraction(fromImproperFraction(numerator, denominator));
  }

  function multiplyFractions(a, b) {
    const fa = toImproperFraction(a);
    const fb = toImproperFraction(b);
    const numerator = fa.numerator * fb.numerator;
    const denominator = fa.denominator * fb.denominator;
    return reduceFraction(fromImproperFraction(numerator, denominator));
  }

  function divideFractions(a, b) {
    const fa = toImproperFraction(a);
    const fb = toImproperFraction(b);
    const numerator = fa.numerator * fb.denominator;
    const denominator = fa.denominator * fb.numerator;
    return reduceFraction(fromImproperFraction(numerator, denominator));
  }

  function generateRandomMathProblems(count, operator, level) {
    const problems = [];
    const usedPairs = new Set();

    while (problems.length < count) {
      let a, b, result;

      if (mode === "fraction") {
        // For fractions, use the same min/max as easy integer for now
        a = getRandomNumber(1, 9);
        b = getRandomNumber(1, 9);
        switch (operator) {
          case "+":
            result = addFractions(a, b);
            break;
          case "-":
            // Ensure a >= b for positive result
            const fa = toImproperFraction(a);
            const fb = toImproperFraction(b);
            if (fa.numerator * fb.denominator < fb.numerator * fa.denominator) {
              [a, b] = [b, a];
            }
            result = subtractFractions(a, b);
            break;
          case "×":
            result = multiplyFractions(a, b);
            break;
          case "÷":
            // Avoid dividing by zero
            if (b.numerator === 0) continue;
            result = divideFractions(a, b);
            break;
        }
        let key = `${writeNumber(a)}-${writeNumber(b)}-${operator}`;
        if (!usedPairs.has(key)) {
          usedPairs.add(key);
          problems.push({ a, b, operator, result, key });
        }
        continue;
      }

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
          a = Number((a * b).toFixed(4));
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
    class=" mb-14 print:mb-12 gap-4 grid lg:flex lg:gap-x-6 text-lg print:flex print:flex-wrap print:text-base"
  >
    <div>
      <div class="flex rounded overflow-hidden justify-center">
        {#each ["+", "-", "×", "÷"] as i}
          <button
            class="py-0.5 px-3 text-center border-r bg-gray-200 border-gray-300 last:border-0 cursor-pointer hover:bg-gray-300 first:rounded-l last:rounded-r"
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
      <div class="flex rounded overflow-hidden justify-center">
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
      <div class="flex rounded overflow-hidden justify-center">
        {#each [{ label: "1", value: "integer" }, { label: "0.1", value: "decimal" }, { label: "½", value: "fraction" }] as i}
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
        ><svg
          class="w-6 mr-2"
          xmlns="http://www.w3.org/2000/svg"
          viewBox="0 0 24 24"
          ><g
            fill="none"
            stroke="currentColor"
            stroke-linecap="round"
            stroke-linejoin="round"
            stroke-width="1.5"
            ><path
              d="M19 10V5a1 1 0 0 0-1-1H6a1 1 0 0 0-1 1v5m15 0H4a1 1 0 0 0-1 1v8a1 1 0 0 0 1 1h16a1 1 0 0 0 1-1v-8a1 1 0 0 0-1-1"
            /><path
              d="M17.5 20v-3a1 1 0 0 0-1-1H11a1 1 0 0 0-1 1v3m-4-7h2"
            /></g
          ></svg
        > Print
      </button>
    </div>
  </div>

  <div class="text-2xl grid sm:grid-cols-2 gap-9 gap-x-14">
    {#each problems as p, index}
      <div class=" flex gap-4 items-center">
        {#if shuffle[index] == 0}
          {#key p.key}
            <AnswerField answer={p.a} />
          {/key}
        {:else}
          <div>
            {#if mode === "fraction" && typeof p.a === "object"}
              <FractionDisplay
                numerator={p.a.numerator}
                denominator={p.a.denominator}
              />
            {:else}
              {writeNumber(p.a)}
            {/if}
          </div>
        {/if}

        <div>{p.operator}</div>

        {#if shuffle[index] == 1}
          {#key p.key}
            <AnswerField answer={p.b} />
          {/key}
        {:else}
          <div>
            {#if mode === "fraction" && typeof p.b === "object"}
              <FractionDisplay
                numerator={p.b.numerator}
                denominator={p.b.denominator}
              />
            {:else}
              {writeNumber(p.b)}
            {/if}
          </div>
        {/if}

        <div>=</div>
        <div class="hidden">{p.result}</div>
        {#if shuffle[index] == 2}
          {#key p.key}
            <AnswerField answer={p.result} />
          {/key}
        {:else}
          <div>
            {#if mode === "fraction" && typeof p.result === "object"}
              <FractionDisplay
                numerator={p.result.numerator}
                denominator={p.result.denominator}
              />
            {:else}
              {writeNumber(p.result)}
            {/if}
          </div>
        {/if}
      </div>
    {/each}
  </div>
</div>
