<script lang="ts">
  import { quintOut } from "svelte/easing";
  import { crossfade } from "svelte/transition";
  import { flip } from "svelte/animate";
  import { select_option } from "svelte/internal";

  const generateRandomArr = (
    length: number,
    max: number,
    min: number
  ): number[] => {
    const generatedArr: number[] = [];
    //solution by following video: https://www.youtube.com/watch?v=giHb-w49yGU
    while (generatedArr.length < length) {
      const randomEl = Math.floor(Math.random() * (max - min + 1)) + min;
      if (generatedArr.indexOf(randomEl) === -1) generatedArr.push(randomEl);
    }
    return generatedArr;
  };

  let active = 0;
  let comparing = [0, 1];
  let finished = false;
  let randomArr: number[] = generateRandomArr(20, 1, 1000);
  let maxWidth = 500;

  const [send, receive] = crossfade({
    duration: (d) => Math.sqrt(d * 200),

    fallback(node, params) {
      const style = getComputedStyle(node);
      const transform = style.transform === "none" ? "" : style.transform;

      return {
        duration: 600,
        easing: quintOut,
        css: (t) => `
					transform: ${transform} scale(${t});
					opacity: ${t}
				`,
      };
    },
  });

  const onBubbleSort = () => {
    setTimeout(() => {
      for (let i = 0; i < randomArr.length; i++) {
        active++;
        //active = active;
        setTimeout(() => {
          for (let j = i; j <= randomArr.length; j++) {
            if (randomArr[i] > randomArr[j]) {
              const tmp: number = randomArr[i];
              randomArr[i] = randomArr[j];
              //   randomArr = randomArr;
              randomArr[j] = tmp;
              setTimeout(() => {
                randomArr = randomArr;
                //comparing = [j, i];
              }, j * 100);
            }
            //comparing = [j, i];
          }
        }, 1000 * i);
      }
    }, 500);
  };

  const mergeSortHelper = (startIdx, endIdx, midIdx) => {
    const length1 = midIdx + 1 - startIdx;
    const length2 = endIdx - midIdx;

    const arr1: number[] = Array.from({ length: length1 });
    const arr2: number[] = Array.from({ length: length2 });

    for (let i = 0; i < length1; i++) {
      arr1.push(randomArr[i + startIdx]);
    }
    for (let i = 0; i < length2; i++) {
      arr2.push(randomArr[i + midIdx + 1]);
    }

    let i = 0;
    let j = 0;
    let k = 0;

    while (i < length1 && j < length2) {
      if (arr1[i] > arr2[j]) {
        randomArr[k] = arr2[j];
        randomArr = randomArr;
        j++;
      } else {
        randomArr[k] = arr1[i];
        randomArr = randomArr;
        i++;
      }
      k++;
    }

    while (i < length1) {
      randomArr.push(arr1[i]);
      i++;
      k++;
    }
    while (j < length2) {
      randomArr.push(arr2[j]);
      j++;
      k++;
    }
  };

  const onMergeSort = (startIdx, endIdx) => {
    if (startIdx < endIdx) {
      const midIdx: number = (endIdx - startIdx) / 2 + startIdx;
      onMergeSort(startIdx, midIdx);
      onMergeSort(midIdx + 1, endIdx);
      mergeSortHelper(startIdx, endIdx, midIdx);
    }
  };

  const sleep = async (ms) => {
    return new Promise((resolve) => setTimeout(resolve, ms));
  };

  const swap = async (i, j) => {
    await sleep(Math.min(j, i) * 10);

    const tmp: number = randomArr[i];
    randomArr[i] = randomArr[j];
    randomArr[j] = tmp;
    // setTimeout(() => (randomArr = randomArr), j * 100);
    randomArr = randomArr;
  };

  const partition = async (startIdx, endIdx) => {
    let pivot: number = randomArr[endIdx];
    let tmp;

    let i = startIdx - 1;

    // setTimeout(() => {
    for (let j = startIdx; j < endIdx; j++) {
      // setTimeout(() => {
      if (randomArr[j] < pivot) {
        i++;
        tmp = j;
        // setTimeout(() => swap(i, j), endIdx * 100);
        await swap(i, j);
      }
      // }, i * 1000);
    }
    // }, 500);

    // setTimeout(() => swap(i + 1, endIdx), endIdx * 100);
    await swap(i + 1, endIdx);
    return i + 1;
  };

  const onQuickSort = async (startIdx, endIdx) => {
    if (startIdx < endIdx) {
      let pi = await partition(startIdx, endIdx);
      // setTimeout(() => {
      await onQuickSort(startIdx, pi - 1);
      await onQuickSort(pi + 1, endIdx);

      // Promise.all([onQuickSort(startIdx, pi - 1), onQuickSort(pi + 1, endIdx)]); // }, Math.min(startIdx, endIdx) * Math.max(startIdx, endIdx) * 50);
    }
  };
</script>

<div class="array-wrapper">
  {#each randomArr as currElement, i (currElement)}
    <div
      in:send={{ key: currElement }}
      out:send={{ key: currElement }}
      animate:flip={{ duration: 200 }}
      class="array-element-bar"
      class:active={active === i}
      class:comparing={comparing[0] === i || comparing[1] === i}
      class:finished={finished === true}
      style="height: {currElement}px; width: {maxWidth / randomArr.length}px"
    >
      {currElement}
    </div>
  {/each}
</div>

<button class="btn--bubble-sort" on:click={onBubbleSort}> Bubble sort </button>
<button
  class="btn--merge-sort"
  on:click={() => onMergeSort(0, randomArr.length - 1)}
>
  Merge sort
</button>
<button
  class="btn--quick-sort"
  on:click={() => onQuickSort(0, randomArr.length - 1)}
>
  Quick sort
</button>

<style>
  .array-wrapper {
    max-width: 100px;
    display: flex;
    flex-direction: row;
    gap: 10px;
  }
  .array-element-bar {
    background-color: red;
  }
  .active {
    background-color: blue;
  }
  .comparing {
    background-color: black;
  }
  .finished {
    background-color: green;
  }
</style>
