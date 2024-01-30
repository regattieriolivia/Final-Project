<script>
  //Card Komponente wird importiert
  import Card from "./Card.svelte";
  import factStore from "../stores/store-facts";

  //Array mit Früchten/Gemüse wird erstellt
  let fruits = [
    "katze1",
    "katze2",
    "katze3",
    "katze4",
    "katze5",
    "katze6",
    "katze7",
    "katze8",
    "katze9",
    "katze10",
  ];
  fruits = fruits.concat(fruits);
  for (let i = fruits.length - 1; i > 0; i--) {
    const j = Math.floor(Math.random() * (i + 1));
    [fruits[i], fruits[j]] = [fruits[j], fruits[i]];
  }
  let showGameboard = false;
  let gameEnded = false;
  let match = "";
  let counter = 0;
  let matchesCounter = 0;
  function startGame() {
    let cards = Array.from(document.querySelectorAll(".card"));

    // Alle ausgewählten Karten auf "open" setzen
    cards.forEach((card) => {
      card.classList.add("open");
    });

    console.log(cards);
    counter = 0;
    showGameboard = true;
  }
  function cancelGame() {
    showGameboard = false;
  }
  //Variable für die offene Karte wird angelegt
  let openCard = null;

  let catFacts = [];

  async function fetchCatFact() {
    try {
      const response = await fetch("https://catfact.ninja/fact");
      if (!response.ok) {
        throw new Error("Network response was not ok");
      }
      const data = await response.json();
      return data.fact;
    } catch (error) {
      console.error("Error fetching cat fact:", error);
      return "Error fetching cat fact";
    }
  }
  for (let i = 0; i < 10; i++) {
    catFacts.push(fetchCatFact());
  }
  let catFactVisible = false;
  let currentCatFact = "";
  let turnEnd = false;
  let cardsMatch = false;

  function checkFinished() {
    console.log(document.getElementsByClassName("finished").length);
    if (document.getElementsByClassName("finished").length == 20) {
      showGameboard = false;
      gameEnded = true;
    }
  }

  function showCatFact() {
    // Zufälligen Katzenfakt auswählen
    let randomIndex = Math.floor(Math.random() * catFacts.length);
    console.log(catFacts[randomIndex]);
    catFacts[randomIndex].then((result) => {
      currentCatFact = result;
      factStore.set([...$factStore, currentCatFact])
      // Pop-up anzeigen
      catFactVisible = true;
    });
    // Pop-up anzeigen
  }

  function closeCatFact() {
    // Pop-up schließen
    catFactVisible = false;
  }
  function resetVariables() {
    setTimeout(() => {
      turnEnd = false;
      cardsMatch = false;
    }, 2000);
  }

  //Funktion für das Klicken auf eine Karte
  function handleCardClick(clickedCard) {
    //Hier wird die Karte ausgewählt, die geklickt wurde
    console.log(clickedCard);
    clickedCard = clickedCard.target;
    clickedCard.style.scale = "1";

    //Karte wird kurz geöffnet

    clickedCard.style.backgroundImage =
      "url(src/img/" + clickedCard.id + ".png)";
    clickedCard.style.backgroundSize = "cover";
    clickedCard.className = "card open s-Lch04uo3Z9wT";
    clickedCard.removeEventListener("click", handleCardClick);
    console.log(clickedCard);

    //Wenn bereits eine Karte offen ist
    if (openCard) {
      //Wenn die Karte, die geklickt wurde, die gleiche ist wie die, die bereits offen ist
      if (openCard.id === clickedCard.id) {
        //Karten verschwinden nach 300ms
        match = "Match!";
        setTimeout(function () {
          clickedCard.className = "card finished";
          openCard.className = "card finished";
          openCard.style.visibility = "hidden";
          clickedCard.style.visibility = "hidden";
          //Offene Karte wird zurückgesetzt
          openCard = null;
          checkFinished();
        }, 600);
        setTimeout(function () {
          match = "";
        }, 2000);
        matchesCounter++;
        turnEnd = true;
        cardsMatch = true;
        resetVariables();
      }
      //Wenn die Karte, die geklickt wurde, nicht die gleiche ist wie die, die bereits offen ist
      else {
        match = "Kein Match!";
        //Karten werden nach 300ms wieder geschlossen
        setTimeout(function () {
          clickedCard.className = "card closed";
          openCard.className = "card closed";
          openCard.style.backgroundImage = "url(src/img/cover.jpeg)";
          openCard.style.scale = "0.8995983936";
          clickedCard.style.backgroundImage = "url(src/img/cover.jpeg)";
          clickedCard.style.scale = "0.8995983936";
          //Offene Karte wird zurückgesetzt
          openCard = null;
        }, 600);
        setTimeout(function () {
          match = "";
        }, 2000);
        turnEnd = true;
        resetVariables();
      }
      counter++;
    }
    //Wenn noch keine Karte offen ist
    else {
      //Geklickte Karte wird als offene Karte gesetzt
      openCard = clickedCard;
    }
  }
</script>

{#if !showGameboard}
  {#if gameEnded}
    <h2>Spiel beendet</h2>
    <button on:click={startGame}>Restart Game</button>
  {:else}
    <!-- Startbutton anzeigen -->
    <button on:click={startGame}>Start Game</button>
  {/if}
{:else}
  <!-- Gameboard anzeigen, wenn der Startbutton gedrückt wurde -->
  <div id="container">
    <div id="gameboard" style="perspective: 100vh;">
      {#each fruits as fruit}
        <Card {fruit} {handleCardClick} />
      {/each}
    </div>
    <div id="containerRightOut">
      <div id="containerRight">
        <h2  style="margin: 0;">Moves: {counter}</h2>
        <h2  style="margin: 0; margin-bottom: 3rem">Matches: {matchesCounter}</h2>
        <button id="catButton" on:click={showCatFact}>Random cat fact</button>
        <button on:click={cancelGame}>Cancel</button>
        <div class="facts">
          {#if $factStore.length > 0}
            {#each $factStore as fact}
              <p>{fact}</p>
            {/each} 
          {:else}
            <p>No fact yet</p>
          {/if}
        </div>
      </div>
      <h2>{match}</h2>
    </div>
    <!-- Pop-up für Katzenfakten -->
    {#if catFactVisible}
      <div id="catFactPopup">
        <p>{currentCatFact}</p>
        <button on:click={closeCatFact}>Close</button>
      </div>
    {/if}
  </div>
{/if}

<style>
  @media (min-width: 600px) {
    #gameboard {
      display: grid;
      grid-template-columns: repeat(5, 1fr);
      grid-template-rows: repeat(4, 1fr);
    }
  }

  #container {
    display: flex;
    flex-direction: row;
    width: 100%;
    justify-content: space-between;
    padding: 2vw;
  }
  #catButton {
    display: flex;
    align-items: center;
    justify-content: center;
    height: 50px;
    width: 150px;
  }
  #catFactPopup {
    position: fixed;
    top: 13%;
    right: 0;
    width: 20vw;
    background-color: white;
    padding: 20px;
    border: 2px solid black;
    z-index: 9999;
    display: flex;
    flex-direction: column;
  }

  .facts {
    margin-top: 2rem;
    width: 100%;
    max-width: 150px;
    height: 200px;
    max-height: 200px;
    overflow-x: hidden;
    overflow-y: auto;
    border-radius: 1rem;
    padding: 0.25rem;
    border: 1px solid white;
    padding: .5rem 1rem;
    /* ... */
  }

  .facts > p {
    font-size: 11px;
    width: unset;
    text-align: left;
    line-height: 1.5;
  }

  #catFactPopup p {
    margin-bottom: 10px;
  }
  #matchPopup {
    position: fixed;
    top: 50%;
    left: 0;
    width: 100%;
    background-color: white;
    padding: 20px;
    border: 2px solid black;
    z-index: 9999;
    display: flex;
    flex-direction: column;
  }
  #containerRight {
    display: flex;
    flex-direction: column;
  }
  #containerRightOut {
    display: flex;
    flex-direction: column;
    justify-content: space-between;
  }
  h2 {
    color: white;
    text-align: left;
  }
  button {
    margin: 5px;
  }
</style>
