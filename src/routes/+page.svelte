<script>
    let money = $state(0);

    function increaseMoney() {
        money += 1;
    }
</script>

<main>
    <h1>Scrappy manages the B's</h1>

    <div class="stats-container">
        <div>Energy: </div>
        <div>Money: {money}</div>
        <div>Community trust: </div>
        <div>Influence: </div>
    </div>

    <div class="buttons-conatiner">
        <button onclick={increaseMoney}>Click for money</button>
    </div>
</main>

<style>
    main {

    }
</style>