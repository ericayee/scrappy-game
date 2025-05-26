<script>
    let money = $state(0);
    let moneyPerSecond = $state(1)
    let energy = $state(50);
    let community = $state(0);
    let playDeadActive = $state(false)

    const autoIncrementMoney = setInterval(() => {
        money += moneyPerSecond;
    }, 1000);

    function increaseMoney() {
        money += 100;
    }

    function increaseEnergy() {
        energy = Math.min(100, energy += 100);
    }

    function increaseCommunity() {
        community += 100;
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
    let stadiumUpgradeCosts = [ 20, 100, 200, 300, 500 ]
    let stadiumStatus = $state(0);
    function upgradeStadium() {
        if ( stadiumStatus < 5 ) {
            money -= stadiumUpgradeCosts[stadiumStatus];
            moneyPerSecond += 2;
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
            moneyPerSecond += 10;
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
                energy = Math.min(100, energy += 50);
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
            // money - 500
            money -= 500;
            // energy - 10
            energy -= 10;
            // community + 120 
            community += 120;

            boughtOneTime1 = true;
        } else if ( num == 2 ) {
            // energy - 20
            energy -= 20;
            // community + 100
            community += 100;

            boughtOneTime2 = true;
        } else if ( num == 3 ) {
            // money - 750
            money -= 750;
            // energy - 10
            energy -= 10;
            // community + 150
            community += 150;

            boughtOneTime3 = true;
        } else if ( num == 4 ) {
            // money - 1000
            money -= 1000;
            // energy - 15
            energy -= 15;
            // community + 200
            community += 200;

            boughtOneTime4 = true;
        }
    }

    function fundraise() {
        energy -= 10;
        money += 50;
    }

    // STATUES
    let boughtRaimondi = $state(false);
    let boughtCityHall = $state(false);
    let boughtLake = $state(false);

    function buyStatue( setting ) {
        if ( setting == 'raimondi' ) {
            boughtRaimondi = true;
        } else if ( setting == 'cityhall' ) {
            boughtCityHall = true;
        } else if ( setting == 'lake' ) {
            boughtLake = true;
        }

        community -= 300;
    }
    
</script>

<main>
<section>
    <h1>Scrappy’s Statue Quest</h1>
    <p>Click enough to place Scrappy statues around Oakland.</p>

    <div class="statues-container">
        <div class="statues-images-container">
            <div class="statue">
            {#if boughtRaimondi}
                <img src="raimondi_revealed.jpg" alt="opaque crowd at Oakland Ballers Game at Raimondi Park with Scrappy silhouette in the background" />
            {:else}
                <img src="raimondi_dark.jpg" alt="crowd at Oakland Ballers Game at Raimondi Park with Scrappy drawing in the background" />
                <button onclick={() => buyStatue('raimondi')} disabled={playDeadActive || money < 300 || community < 300}>
                    Buy Raimondi Park statue
                    <p>Cost: 💰300 ❤️300</p>
                </button>
            {/if}
            </div>
            <div class="statue">
                {#if boughtCityHall}
                    <img src="cityhall_revealed.jpg" alt="Oakland City Hall plaza with small crowd and Scrappy statue drawing in foreground" />
                {:else}
                    <img src="cityhall_dark.jpg" alt="opaque Oakland City Hall plaza with small crowd and Scrappy statue drawing in foreground" />
                    <button onclick={() => buyStatue('cityhall')} disabled={playDeadActive || money < 300 || community < 300}>
                        Buy City Hall statue
                        <p>Cost: 💰300 ❤️300</p>
                    </button>
                {/if}
            </div>
            <div class="statue">
                {#if boughtLake}
                    <img src="lake_revealed.jpg" alt="Oakland Lake Merritt at night with lit buildings and Scrappy statue drawing in the water" />
                {:else}
                    <img src="lake_dark.jpg" alt="opaque Oakland Lake Merritt at night with lit buildings and Scrappy statue drawing in the water" />
                    <button onclick={() => buyStatue('lake')} disabled={playDeadActive || money < 300 || community < 300}>
                        Buy Lake Merritt statue
                        <p>Cost: 💰300 ❤️300</p>
                    </button>
                {/if}
            </div>
        </div>
        {#if boughtCityHall && boughtLake && boughtRaimondi}
            <h3>Congratulations, you got all the statues! Scrappy's influence grows. Thanks for playing.</h3>
        {/if}
    </div>

    <div class="stats-container">
        <p>💰 Money ({moneyPerSecond}/second): {money.toLocaleString()}</p>
        <p>⚡ Energy: {energy}/100</p>
        <p>❤️ Community love: {community}</p>
    </div>

    <!-- <button onclick={increaseMoney}>CHEAT: Click for money</button>
    <button onclick={increaseEnergy} disabled={energy == 100}>CHEAT: Click for energy</button>
    <button onclick={increaseCommunity}>CHEAT: Click for community</button> -->

    <div class="clicking-container">
        <div class="clicking-section">
            <h3>Scrappy rally!</h3>
            <div class="buttons-section">
                <button onclick={nextGameIsHome ? rallyHome : rallyAway} disabled={playDeadActive || !isGamePlaying || ( !nextGameIsHome && energy < 2 ) || ( nextGameIsHome && energy < 1 )}>
                    Rally the crowd at the {nextGameIsHome ? 'home' : 'away'} game
                    {#if nextGameIsHome}
                        <p>Cost: ⚡1</p>
                        <p>Reward: ❤️2</p>
                    {:else}
                        <p>Cost: ⚡2</p>
                        <p>Reward: ❤️1</p>
                    {/if}
                    <p style:font-style={'italic'} style:margin-top={'8px'}>{isGamePlaying ? 'Game over in' : 'Next game in'}: {gameCountdown}</p>
                </button>
                <button onclick={fundraise} disabled={playDeadActive || energy < 10}>
                    Fundraise
                    <p>Cost: ⚡10</p>
                    <p>Reward: 💰50</p>
                </button>
                <button onclick={playDead} disabled={playDeadActive}>
                    Play dead (AKA nap)
                    <p>Cost: Can’t rally, fundraise or<br/>buy one-time actions for 15 seconds</p>
                    <p>Reward: ⚡50</p>
                    <p style:font-style={'italic'} style:height={'17.6px'} style:margin-top={'8px'}>{playDeadActive ? `Play dead over in: ${playDeadCounter}` : ''}</p>
                </button>
            </div>
        </div>
        <div class="clicking-section">
            <h3>Stadium upgrades</h3>
            <div class="buttons-section">
                <button onclick={upgradeStadium} disabled ={stadiumStatus == 5 || ( stadiumStatus < 5 && money < stadiumUpgradeCosts[stadiumStatus] )}>
                    Add stadium seating
                    {#if stadiumStatus < 5}
                        <p>More seats = more tickets to sell</p>
                    {:else}
                        <p>Sold out!</p>
                    {/if}
                    <p>Owned: {stadiumStatus}/5{stadiumStatus == 5 ? ' ✅' : ''}</p>
                    {#if stadiumStatus < 5}
                    <p>Cost: 💰{stadiumUpgradeCosts[stadiumStatus]}</p>
                    <p>Reward: +2 💰/second</p>
                    {/if}
                </button>
                {#if stadiumStatus > 0}
                    <button onclick={upgradeConcessions} disabled={concessionStatus == 3 || ( concessionStatus < 3 && money < concessionUpgrades[concessionStatus].cost )}>
                        Concessions upgrade
                        {#if concessionStatus < 3}
                            <p>Next: {concessionUpgrades[concessionStatus].name}</p>
                        {:else}
                            <p>Yummm</p>
                        {/if}
                        <p>Owned: {concessionStatus}/3{concessionStatus == 3 ? ' ✅' : ''}</p>
                        {#if concessionStatus < 3}
                            <p>Cost: 💰{concessionUpgrades[concessionStatus].cost}</p>
                            <p>Reward: +10 💰/second</p>
                        {/if}
                    </button>
                {/if}
            </div>
        </div>
        <div class="clicking-section">
            <h3>One-time actions</h3>
            <div class="buttons-section">
                <button onclick={() => oneTime(2)} disabled={playDeadActive || boughtOneTime2 || energy < 20}>
                Host neighborhood cleanup{boughtOneTime2 ? ' ✅' : ''}
                <p>Cost:⚡20</p>
                <p>Reward: ❤️100</p>
                </button>
                <button onclick={() => oneTime(1)} disabled={playDeadActive || boughtOneTime1 || money < 500 || energy < 10}>
                    Promote discounted tickets{boughtOneTime1 ? ' ✅' : ''}
                    <p>Cost: 💰 500 ⚡10</p>
                    <p>Reward: ❤️120</p>
                </button>
                <button onclick={() => oneTime(3)} disabled={playDeadActive || boughtOneTime3 || money < 750 || energy < 10}>
                    Give out free swag{boughtOneTime3 ? ' ✅' : ''}
                    <p>Cost: 💰750 ⚡10</p>
                    <p>Reward: ❤️150</p>
                </button>
                <button onclick={() => oneTime(4)} disabled={playDeadActive || boughtOneTime4 || money < 1000 || energy < 15}>
                    Team up with a local celebrity{boughtOneTime4 ? ' ✅' : ''}
                    <p>Cost: 💰1,000 ⚡15</p>
                    <p>Reward: ❤️200</p>
                </button>
            </div>
        </div>
        
    </div>
</section>
</main>

<style>
    main {
        overflow-y: auto;
        height: 600px;
    }
    section {
        margin: 8px;

        @media screen and ( max-width: 700px ){
            text-align: center;
        }

        .stats-container {
            margin: 8px 0;
        }
        .clicking-container {
            padding-bottom: 40px;

            .clicking-section {
                
                .buttons-section {
                    display: flex;
                    flex-wrap: wrap;

                    @media screen and ( max-width: 700px ){
                        justify-content: center;
                    }
                
                    button {
                        margin: 4px 8px 4px 0;

                    }
                }
            }
        }

        .statues-container {
            /* border-top: 2px solid #C8A31E; */
            margin-top: 8px;
            /* padding-top: 2px; */
            
            .statues-images-container {
                display: flex;
                flex-wrap: wrap;
                @media screen and ( max-width: 700px ){
                    justify-content: center;
                }

                .statue {
                    width: 200px;
                    height: 150px;
                    position: relative;
                    margin: 0 8px 8px 0;

                    @media screen and ( max-width: 700px ){
                        margin: 0 8px 16px;
                    }
                    
                    img {
                        width: 200px;
                        z-index: 5;
                    }
                    button {
                        position: absolute;
                        left: 100px;
                        top: 75px;
                        transform: translate(-50%, -50%);
                        width: 150px;
                    }
                }
            }
            
        }
    }
</style>