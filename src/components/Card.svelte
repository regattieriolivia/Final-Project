<script>
  //Farbe der Karte (string), die aus Gameboard.svelte übergeben wird
  export let fruit;
  //Funktion, die aus Gameboard.svelte übergeben wird
  export let handleCardClick;
  let flipped = false;

  //Funktion, die aufgerufen wird, wenn eine Karte geklickt wird
  function handleClick(clickedCard) {
    //Geklickte Karte wird übergeben
    handleCardClick(clickedCard);
  }
</script>

<div
  class="card closed"
  class:flipped
  id={fruit}
  on:click={(e) => {
    handleClick(e);
    flipped = !flipped;
  }}
></div>

<style>
  .closed {
    background-color: lightblue;
    background-image: url("src/img/cover.jpeg");
    background-size: cover;
  }

  .card {
    position: relative !important;
    transform: rotateY(180deg);
    transition: transform 0.4s;
    transform-style: preserve-3d;
    margin: 10px;
    width: 8vw;
    height: 8vw;
    transition: all .2s cubic-bezier(0.785, 0.135, 0.15, 0.86);
  }

  .card.flipped {
    transform: rotateY(0);
  }

  .front,
  .back {
    display: flex;
    align-items: center;
    justify-content: center;
    position: absolute;
    width: 100%;
    height: 100%;
    left: 0;
    top: 0;
    backface-visibility: hidden;
  }
</style>
