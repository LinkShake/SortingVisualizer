<script lang="ts">
  const algorithmsOptions = ["bubbleSort", "mergeSort", "quickSort"];
</script>

<nav class="top-nav-bar">
  <p class="title">SortingVisualizer</p>
  <form class="select-algorithm">
    <select>
      {#each algorithmsOptions as option}
        <option value={option}>{option}</option>
      {/each}
    </select>
  </form>
  <!-- <button class="btn--sort">Sort</button> -->
  <p style="color: black; padding: 0; margin: 0;">
    Change size array and speed
  </p>
  <input type="range" min="2" max="50" class="array-length-selector" />
</nav>

<style>
  .top-nav-bar {
    position: absolute;
    top: 0;
    left: 0;
    background-color: white;
    width: 100%;
    height: 5rem;
  }
  .title {
    color: black;
    font-weight: bold;
    font-size: 20px;
    position: absolute;
    padding: 0.75rem;
    margin: 0;
  }
  .select-algorithm {
    position: absolute;
    right: 0;
    padding: 0.75rem;
  }
  .array-length-selector {
    padding: 1rem;
  }
</style>
