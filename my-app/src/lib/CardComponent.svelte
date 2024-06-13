<script lang="ts">
  import type { IDataModel } from "../utils/testDataModel";
  import { testData } from '../utils/data';

  let flipped = false;
  let history: IDataModel[] = [];
  let currentIndex = -1;
  let selectedData: IDataModel;

  const shuffleArray = (array: IDataModel[]): IDataModel[] => {
    let shuffled = array.slice();
    for (let i = shuffled.length - 1; i > 0; i--) {
      const j = Math.floor(Math.random() * (i + 1));
      [shuffled[i], shuffled[j]] = [shuffled[j], shuffled[i]];
    }
    return shuffled;
  };

  let shuffledData = shuffleArray(testData);

  const setData = (data: IDataModel) => {
    selectedData = data;
  };

  const nextData = () => {
    flipped = false;
    if (currentIndex < shuffledData.length - 1) {
      currentIndex++;
    } else {
      shuffledData = shuffleArray(testData);
      currentIndex = 0;
    }
    const nextItem = shuffledData[currentIndex];
    if (currentIndex < history.length - 1) {
      history.splice(currentIndex + 1);
    }
    history.push(nextItem);
    setData(nextItem);
  };

  const prevData = () => {
    if (currentIndex > 0) {
      currentIndex--;
      setData(history[currentIndex]);
    }
  };

  nextData();

  console.log("testData", testData);
</script>
<div class="container">
  Flip the card
  <button
          class="card"
          class:flipped={flipped}
          on:click={() => flipped = !flipped}
  >
    <div class="front">
      <h1 class="title">{selectedData.name}</h1>
      <p class="text">Building: {selectedData.startBuilding} - {selectedData.endBuilding}</p>
      <p class="text">Location: {selectedData.location}, {selectedData.country}</p>
    </div>
    <div class="back" style="background-image: url({selectedData.imageUrl}); background-size: contain; background-position: center; background-repeat: no-repeat;">
      <div class="pattern"></div>
    </div>
  </button>
  <!--{#if flipped}-->
    <div class="nav-buttons">
      <button on:click={prevData} disabled={currentIndex <= 0}>Prev</button>
      <button on:click={nextData}>Next</button>
    </div>
  <!--{/if}-->
</div>
<style>
  :root {
    --bg-1: hsl(0, 0%, 100%);
    --bg-2: hsl(206, 20%, 90%);
    --bg-3: hsl(206, 20%, 80%);
  }

  .container {
    display: flex;
    flex-direction: column;
    gap: 1em;
    height: 100%;
    align-items: center;
    justify-content: center;
    perspective: 100vh;
  }

  .card {
    position: relative;
    aspect-ratio: 16 / 9;
    font-size: min(1vh, 0.25rem);
    height: 120em;
    background: var(--bg-1);
    border-radius: 2em;
    transform: rotateY(180deg);
    transition: transform 0.4s;
    transform-style: preserve-3d;
    padding: 0;
    user-select: none;
    cursor: pointer;
  }

  .card.flipped {
    transform: rotateY(0);
  }

  .front, .back {
    display: flex;
    align-items: center;
    justify-content: center;
    position: absolute;
    width: 100%;
    height: 100%;
    left: 0;
    top: 0;
    backface-visibility: hidden;
    border-radius: 2em;
    border: 1px solid var(--fg-2);
    box-sizing: border-box;
    padding: 2em;
    color: var(--fg-1);
    font-size: 1.5em;
    flex-direction: column;
    text-align: center;
  }

  .nav-buttons {
    display: flex;
    gap: 40px;
  }

  .front {
    background: var(--bg-2);
  }

  .back {
    transform: rotateY(180deg);
    background-color: var(--bg-2);
  }

  .pattern {
    width: 100%;
    height: 100%;
    border-radius: 1em;
  }

  .title {
    font-size: 5em;
    margin-bottom: 1em;
  }

  .text {
    font-size: 3em;
  }
</style>
