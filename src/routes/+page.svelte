<script>
    // let gameTimer = $state(0);
    let money = $state(0);
    let moneyPerSecond = $state(1)
    let energy = $state(50);
    let community = $state(0);
    let playDeadActive = $state(false)

    // const autoIncrementTime = setInterval(() => {
    //     gameTimer += 1;
    // }, 1000);


    const autoIncrementMoney = setInterval(() => {
        money += moneyPerSecond;
    }, 1000);

    function increaseMoney() {
        money += 100;
    }

    function increaseEnergy() {
        if ( energy < 100 ) {
            energy += 1;
        }
    }

    function increaseCommunity() {
        community += 1;
    }

    // GAME TIME
    // alternates 10 seconds waiting, 10 seconds home game, 10 seconds waiting, 10 seconds away game
    let isGamePlaying = $state(true);
    let nextGameIsHome = $state(true);
    let gameCountdown = $state(10);

    let gameCountingDown = setInterval(() => {
        gameCountdown--;

        if ( gameCountdown < 0 ) {
            gameCountdown = 10;
            isGamePlaying = !isGamePlaying;
            if ( !isGamePlaying ) {
                nextGameIsHome = !nextGameIsHome;
            }
        }
    }, 1000)

    // -1 energy, +2 community
    function rallyHome() {
        energy = Math.max(0, energy - 1);
        community += 2;
    }

    // -2 energy, +1 community
    function rallyAway() {
        energy = Math.max(0, energy - 2);
        community += 1;
    }

    // STADIUM SEATING
    // starts at 0; max 5
    let stadiumUpgradeCosts = [ 10, 100, 500, 1000, 2000 ]
    let stadiumStatus = $state(0);
    function upgradeStadium() {
        if ( stadiumStatus < 5 ) {
            money -= stadiumUpgradeCosts[stadiumStatus];
            moneyPerSecond += 1;
            stadiumStatus += 1;
        }
        
    }

    // STADIUM CONCESSIONS
    // opens when stadiumStatus is at 1
    // 3 levels
    let concessionUpgrades = [ 
        { name: 'Hot dog vendors', cost: 50 }, 
        { name: 'A few food stands', cost: 500},
        { name: 'A whole food truck plaza', cost: 750}
     ]
    let concessionStatus = $state(0);
    function upgradeConcessions() {
        if ( concessionStatus < 3 ) {
            money -= concessionUpgrades[concessionStatus].cost;
            moneyPerSecond += 3;
            concessionStatus += 1;
        }
        
    }

    // PLAY DEAD AKA NAP
    let playDeadCounter = $state(15);
    function playDead() {
        playDeadActive =  true;
        const playDeadInterval = setInterval(() => {
            playDeadCounter--;

            if ( playDeadCounter < 0) {
                clearInterval( playDeadInterval );
                playDeadActive = false;
                playDeadCounter = 15;
                energy = Math.min(100, energy += 25);
            }
        }, 1000);
    }

    // ONE-TIME PURCHASES
    let boughtOneTime1 = $state(false);
    let boughtOneTime2 = $state(false);
    let boughtOneTime3 = $state(false);
    let boughtOneTime4 = $state(false);

    function oneTime( num ) {
        if ( num == 1 ) {
            // money - 1000
            money -= 1000;
            // energy - 10
            energy -= 10;
            // community + 40 
            community += 40;

            boughtOneTime1 = true;
        } else if ( num == 2 ) {
            // energy - 20
            energy -= 20;
            // community + 100
            community += 100;

            boughtOneTime2 = true;
        } else if ( num == 3 ) {
            // money - 2000
            money -= 2000;
            // energy - 10
            energy -= 10;
            // community + 100
            community += 100;

            boughtOneTime3 = true;
        } else if ( num == 4 ) {
            // money - 2500
            money -= 2500;
            // energy - 15
            energy -= 15;
            // community + 150
            community += 150;

            boughtOneTime4 = true;
        }
    }

    function fundraise() {
        energy -= 10;
        money += 100;
    }
    
</script>

<main>
<section>
    <h1>Scrappy manages the B’s</h1>
    <p>Goal: Grow your community love!</p>

    <div class="stats-container">
        <p>💰 Money ({moneyPerSecond}/second): {money.toLocaleString()}</p>
        <p>⚡ Energy: {energy}/100</p>
        <p>❤️ Community love: {community}</p>
    </div>

    <!-- <button onclick={increaseMoney}>CHEAT: Click for money</button>
    <button onclick={increaseEnergy} disabled={energy == 100}>CHEAT: Click for energy</button>
    <button onclick={increaseCommunity}>CHEAT: Click for community</button> -->

    <div class="buttons-container">
        <div class="buttons-container-col">
            <h3>Scrappy do your thing</h3>
            <button onclick={nextGameIsHome ? rallyHome : rallyAway} disabled={playDeadActive || !isGamePlaying || ( !nextGameIsHome && energy < 2 ) || ( nextGameIsHome && energy < 1 )}>
                Rally the crowd at today's {nextGameIsHome ? 'home' : 'away'} game
                {#if nextGameIsHome}
                    <p>Cost: ⚡ 1</p>
                    <p>Reward: ❤️ 2</p>
                {:else}
                    <p>Cost: ⚡ 2</p>
                    <p>Reward: ❤️ 1</p>
                {/if}
                <p style:font-style={'italic'} style:margin-top={'8px'}>{isGamePlaying ? 'Game over in' : 'Next game in'}: {gameCountdown}</p>
            </button>

            <button onclick={playDead} disabled={playDeadActive}>
                Play dead (AKA nap)
                <p>Cost: Can’t rally, fundraise or buy one-time actions</p>
                <p>Reward: ⚡ 25</p>
                {#if playDeadActive}
                    <p style:font-style={'italic'} style:margin-top={'8px'}>Play dead over in: {playDeadCounter}</p>
                {/if}
            </button>
            
            <button onclick={fundraise} disabled={playDeadActive || energy < 10}>
                Fundraise
                <p>Cost: ⚡ 10</p>
                <p>Reward: 💰 100</p>
            </button>

            <h3>One-time actions</h3>
            <div class="buttons-row">
                <button onclick={() => oneTime(2)} disabled={playDeadActive || boughtOneTime2 || energy < 20}>
                Host neighborhood cleanup{boughtOneTime2 ? ' ✅' : ''}
                <p>Cost:⚡ 20</p>
                <p>Reward: ❤️ 100</p>
                </button>
                <button onclick={() => oneTime(1)} disabled={playDeadActive || boughtOneTime1 || money < 1000 || energy < 10}>
                    Promote discounted tickets{boughtOneTime1 ? ' ✅' : ''}
                    <p>Cost: 💰 1,000 ⚡ 10</p>
                    <p>Reward: ❤️ 40</p>
                </button>
                <button onclick={() => oneTime(3)} disabled={playDeadActive || boughtOneTime3 || money < 2000 || energy < 10}>
                    Give out free swag{boughtOneTime3 ? ' ✅' : ''}
                    <p>Cost: 💰 2,000 ⚡ 10</p>
                    <p>Reward: ❤️ 100</p>
                </button>
                <button onclick={() => oneTime(4)} disabled={playDeadActive || boughtOneTime4 || money < 2500 || energy < 15}>
                    Bring a local celebrity to visit{boughtOneTime4 ? ' ✅' : ''}
                    <p>Cost: 💰 2,500 ⚡ 15</p>
                    <p>Reward: ❤️ 150</p>
                </button>
            </div>
        </div>
        <div class="buttons-container-col">
            <h3>Stadium upgrades</h3>
            <button onclick={upgradeStadium} disabled ={stadiumStatus == 5 || ( stadiumStatus < 5 && money < stadiumUpgradeCosts[stadiumStatus] )}>
                Add stadium seating
                <p>Owned: {stadiumStatus}/5</p>
                {#if stadiumStatus < 5}
                <p>Cost: 💰 {stadiumUpgradeCosts[stadiumStatus]}</p>
                <p>Reward: +1 money rate</p>
                {/if}
            </button>
            {#if stadiumStatus > 0}
                <button onclick={upgradeConcessions} disabled={concessionStatus == 3 || ( concessionStatus < 3 && money < concessionUpgrades[concessionStatus].cost )}>
                    Concessions upgrade
                    {#if concessionStatus < 3}
                        <p>Next: {concessionUpgrades[concessionStatus].name}</p>
                    {/if}
                    <p>Owned: {concessionStatus}/3</p>
                    {#if concessionStatus < 3}
                        <p>Cost: 💰 {concessionUpgrades[concessionStatus].cost}</p>
                        <p>Reward: +5 money rate</p>
                    {/if}
                </button>
            {/if}

            <img src="scrappy_transparent.png" alt="line drawing of Scrappy the Rally Possum facing sideways" />
        </div>
    </div>
    
    </section>
</main>

<style>
    section {
        margin: 0 auto;
        max-width: 700px;
        

        .stats-container {
            margin: 8px 0 24px;

            border-top: 2px solid #C8A31E;
            border-bottom: 2px solid #C8A31E;
            padding: 4px;
        }

        .buttons-container {
            display: flex;
            justify-content: space-between;

            .buttons-container-col {
                display: flex;
                flex-direction: column;
                max-width: calc(50% - 4px);

                .buttons-row {
                    display: flex;
                    flex-wrap: wrap;

                    button {
                        min-width: 250px;
                    }
                }
            }

            .buttons-container-col:last-of-type {
                align-items: end;
            }

            button {
                margin-bottom: 16px;
                width: fit-content;
            }
        }

        img {
            width: 250px;
        }
    }
</style>