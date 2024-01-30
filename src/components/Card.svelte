<script>
    //Farbe der Karte (string), die aus Gameboard.svelte übergeben wird
    export let fruit;
    //Funktion, die aus Gameboard.svelte übergeben wird
    export let handleCardClick;

    //Funktion, die aufgerufen wird, wenn eine Karte geklickt wird
    function handleClick(clickedCard) {
        //Geklickte Karte wird übergeben
        handleCardClick(clickedCard);
    }
  </script>
  
  <div class="card closed" id={fruit} style="margin:10px; width:8vw; height:8vw;" on:click={handleClick}></div>

  <style>
    .closed {
      background-color: lightblue;
      background-image: url(src/img/cover.jpeg);
      background-size: cover;
    }
  </style>
  