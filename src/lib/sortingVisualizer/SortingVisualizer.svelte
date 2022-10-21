<script lang="ts">
  import { quintOut } from "svelte/easing";
  import { crossfade } from "svelte/transition";
  import { flip } from "svelte/animate";

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

  const generateRandomArr = (
    length: number,
    max: number,
    min: number
  ): number[] => {
    const generatedArr: number[] = [];
    for (let i = 0; i <= length; i++)
      generatedArr.push(Math.floor(Math.random() * (max - min + 1)) + min);
    return generatedArr;
  };

  const onBubbleSort = () => {
    setTimeout(() => {
      for (let i = 0; i < randomArr.length; i++) {
        setTimeout(() => {
          for (let j = i; j <= randomArr.length; j++) {
            if (randomArr[i] > randomArr[j]) {
              const tmp: number = randomArr[i];
              randomArr[i] = randomArr[j];
              //   randomArr = randomArr;
              randomArr[j] = tmp;
              setTimeout(() => (randomArr = randomArr), j * 100);
            }
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

  let randomArr: number[] = generateRandomArr(5, 1, 1000);
</script>

<div class="array-wrapper">
  {#each randomArr as currElement (currElement)}
    <div
      in:send={{ key: currElement }}
      out:send={{ key: currElement }}
      animate:flip={{ duration: 200 }}
      class="array-element-bar"
      style="height: {currElement}px;"
    >
      {currElement}
    </div>
  {/each}
</div>

<button class="btn--bubble-sort" on:click={onBubbleSort}> Bubble sort </button>
<button
  class="btn--merge-sort"
  on:click={() => onMergeSort(0, randomArr.length)}
>
  Merge sort
</button>

<style>
  .array-wrapper {
    display: flex;
    flex-direction: row;
    gap: 100px;
  }
  .array-element-bar {
    background-color: red;
  }
</style>
