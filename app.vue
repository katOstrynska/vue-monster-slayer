Vue.component('player', {
    props: {
        name: {
            type: String,
            required: true
        },
        health: {
            type: Number
        }
    },
    template: `<div class="player">
        <h3>{{ name }}</h3>
        <div class="healthBar">
            <div :style="{ width: health + '%', height: '100%', backgroundColor: health > 40 ? '#b7da8a' : '#fff093' }">
                {{ health }}
            </div>
        </div>
    </div>`
});

new Vue({
    el: '#app',
    data: {
        playerHealth: 100,
        monsterHealth: 100,
        gameRunning: false,
        logs: []
    },
    methods: {
        startGame: function() {
            this.gameRunning = true;
            this.playerHealth = 100;
            this.monsterHealth = 100;
            this.logs = [];
        },
        getDamage: function() {
            let damage = this.damageDone(3, 10);
            this.monsterHealth -= damage;

            this.logs.unshift({
                monsterDamaged: true,
                message: `Monster is hit by ${damage}`
            });

            if (this.checkResult()) {
                return;
            }

            this.monsterDamage();            
        },
        dealSpecialDamage: function() {
            let damage = this.damageDone(10, 20);
            this.monsterHealth -= damage;

            this.logs.unshift({
                monsterDamaged: true,
                message: `Monster is hit by ${damage}`
            });

            if (this.checkResult()) {
                return;
            }

            this.monsterDamage();  
        },
        getHeal: function() {
            if (this.playerHealth <= 90) {
                this.playerHealth += 10;
            }
            this.logs.unshift({
                playerHealed: true,
                message: `Player is healed by 10`
            });
            this.monsterDamage();
        },
        monsterDamage: function() {
            let damage = this.damageDone(5, 12);
            this.playerHealth -= damage;
            this.logs.unshift({
                playerDamaged: true,
                message: `Player is hit by ${damage}`
            });
            this.checkResult();
        },
        damageDone: function(min, max) {
            return Math.max(Math.floor(Math.random() * max) + 1, min);
        },
        checkResult: function() {
            if (this.playerHealth <= 0) {
                if(confirm('You lost :( Do You want to play again?')) {
                    this.startGame();
                } else {
                    this.gameRunning = false;
                    this.playerHealth = 0;
                }
                return true;
            } else if (this.monsterHealth <= 0) {
                if(confirm('You win! :D Do You want to play again?')) {
                    this.startGame();
                } else {
                    this.gameRunning = false;
                    this.monsterHealth = 0;
                }
                return true;
            }
            return false;
        }
    }
})