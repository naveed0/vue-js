<template>
<div id="app">
    <section class="row">
        <div class="small-6 columns">
            <h1 class="text-center">YOU</h1>
            <div class="healthbar">
                <div class="healthbar text-center" style="background-color: green; margin: 0; color: white;" 
                :style="{width : playerHealth + '%'}">
                    {{playerHealth}}
                </div>
            </div>
        </div>
        <div class="small-6 columns">
            <h1 class="text-center">MONSTER</h1>
            <div class="healthbar">
                <div class="healthbar text-center" style="background-color: green; margin: 0; color: white;"
                :style="{width : monsterHealth + '%'}">
                  {{monsterHealth}}
                </div>
            </div>
        </div>
    </section>
    <section class="row controls" v-if="!gameisrunning">
        <div class="small-12 columns">
            <button id="start-game" @click="startgame">START NEW GAME</button>
        </div>
    </section>
    <section class="row controls" v-else>
        <div class="small-12 columns">
            <button id="attack" @click="attack">ATTACK</button>
            <button id="special-attack" @click="specialattack">SPECIAL ATTACK</button>
            <button id="heal" @click="heal">HEAL</button>
            <button id="give-up" @click="giveup">GIVE UP</button>
        </div>
    </section>
    <section class="row log" v-if="turns.length >0">
        <div class="small-12 columns">
            <ul>
                <li v-for="turn in turns" v-bind:key = "turn" 
                    :class = "{'player-turn' : turn.isplayer, 'monster-turn' : !turn.isplayer}">
                    {{turn.text}}
                </li>
            </ul>
        </div>
    </section>
</div>
</template>

<script>
  export default({
    data : function (){
      return{
          playerHealth :100,
          monsterHealth : 100,
          gameisrunning : false,
          turns : [],
          isplayer : true
      }
    },
    methods : {
        startgame:function (){
            this.playerHealth = 100;
            this.monsterHealth = 100;
            this.gameisrunning = true;
            this.turns = [];
        },
        attack : function(){
            this.playerattack(3,10);
            if(this.checkwin()){
                return;
            }
            this.monsterattack(3,10);
            this.checkwin();
        },
        specialattack : function(){
            this.playerattack(5,15);
            if(this.checkwin()){
                return;
            }else{
                this.monsterattack(5,15);
                this.checkwin();
            }
        },
        heal : function (){
            if(this.playerHealth <= 90){
                this.playerHealth += 10;
            }else{
                this.playerHealth =100;
            }
            this.monsterattack(3,10);
        },
        giveup : function(){
            this.gameisrunning = false;
            this.playerHealth = 100;
            this.monsterHealth = 100;
        },
        calculatedamage : function(min, max){
           return Math.max(Math.floor(Math.random() * max)+1 , min)
        },
        playerattack: function(max,min){
            var damage = this.calculatedamage(max,min);
            this.monsterHealth -= damage;
            this.turns.unshift({
                text : "Player attacks on monster " + damage,
                isplayer : true
            });
        },
        monsterattack : function(max,min){
            var damage = this.calculatedamage(max,min);
            this.playerHealth -= damage;
            this.turns.unshift({
                text : "Monster ATTACK on player " + damage,
                isplayer : false
            });
        },
        checkwin : function(){
            if(this.monsterHealth <= 0){
                if(confirm("you won ! new game?")){
                    this.startgame();
                }else{
                    this.gameisrunning = false;
                }
                return true;
            }else if(this.playerHealth <= 0){
                if(confirm("You lost ! New game ?")){
                     this.startgame();
                }else{
                    this.gameisrunning = false;
                }
                return true;
            } 
            return false;
        }
    }
  });
</script>

<style>
.text-center {
    text-align: center;
}

.healthbar {
    width: 80%;
    height: 40px;
    background-color: #eee;
    margin: auto;
    transition: width 500ms;
}

.controls, .log {
    margin-top: 30px;
    text-align: center;
    padding: 10px;
    border: 1px solid #ccc;
    box-shadow: 0px 3px 6px #ccc;
}

.turn {
    margin-top: 20px;
    margin-bottom: 20px;
    font-weight: bold;
    font-size: 22px;
}

.log ul {
    list-style: none;
    font-weight: bold;
    text-transform: uppercase;
}

.log ul li {
    margin: 5px;
}

.log ul .player-turn {
    color: blue;
    background-color: #e4e8ff;
}

.log ul .monster-turn {
    color: red;
    background-color: #ffc0c1;
}

button {
    font-size: 20px;
    background-color: #eee;
    padding: 12px;
    box-shadow: 0 1px 1px black;
    margin: 10px;
}

#start-game {
    background-color: #aaffb0;
}

#start-game:hover {
    background-color: #76ff7e;
}

#attack {
    background-color: #ff7367;
}

#attack:hover {
    background-color: #ff3f43;
}

#special-attack {
    background-color: #ffaf4f;
}

#special-attack:hover {
    background-color: #ff9a2b;
}

#heal {
    background-color: #aaffb0;
}

#heal:hover {
    background-color: #76ff7e;
}

#give-up {
    background-color: #ffffff;
}

#give-up:hover {
    background-color: #c7c7c7;
}
</style>
