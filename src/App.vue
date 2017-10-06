<template>
  <div>
    <section class="row">
      <div class="small-6 columns">
        <h1 class="text-center">PLAYER</h1>
        <div class="wins">
          <div v-if="gameStarted" class="wins text-center" style="background-color: green; margin: 0; color: white;" :style="{width: 100*(guessIndex/computerThrowCount) + '%'}">
            {{guessIndex}}/{{computerThrowCount}}
          </div>
        </div>
      </div>
      <div class="small-6 columns">
        <h1 class="text-center">COMPUTER</h1>
        <div class="wins">
          <div v-if="gameStarted" class="wins text-center" style="background-color: green; margin: 0; color: white;" :style="{width: 100*(count/computerThrowCount) + '%'}">
            {{count}}/{{computerThrowCount}}
          </div>
        </div>
      </div>
    </section>
    <section id="computerMoves" class="row controls" style="display: none">
    <span id="numCompleted" class="small-12 columns">
    </span>
      <span>
      <!--<p>Completed {{guessIndex}} / {{computerThrowCount}}</p>-->
      <p>Computer Throw Number: {{count}}</p>
      <img id="image" src="" style="height:80px; width:100px">
    </span>
    </section>
    <section id="startbtn" class="row controls" >
      <div class="small-12 columns">
        <button id="start-game" @click="startNew">START NEW GAME</button>
      </div>
    </section>
    <section id="gamebtns" class="row controls" style="display: none">
      <div class="small-12 columns">
        <button @click="rock" id="rock">ROCK</button>
        <button id="paper" @click="paper">PAPER</button>
        <button id="scissors" @click="scissors">SCISSORS</button>
        <button id="restart" @click="restart">RESTART</button>
      </div>
    </section>
    <section class="row log">
      <div class="small-8 columns">
        <ul v-for="(value,key,index) in log">
          <li v-bind:class="{ 'player-turn': value.playerwin, 'computer-turn': !value.playerwin, 'tie-turn': value.playertie }">
            {{value.msg}}
          </li>
        </ul>
      </div>
      <div class="small-4 columns">
        <ul>
          <li class="tie-turn" v-if="highScoreTime != -1">
            HighScore: </br>{{highScoreRounds}} rounds in {{highScoreTime.toFixed(2)}} seconds
          </li>
          <li class="tie-turn" v-else-if="log.length > 0">
            HighScore: </br>none
          </li>
        </ul>
      </div>
    </section>
  </div>
</template>

<script>
export default {
  name: 'app',
  data () {
    return {
      msg: 'Welcome to Your Vue.js App',
      computerWins: 0,
      userWins: 0,
      ties: 0,
      gameCount: 0,
      width: 0,
      userGuess: "",
      computerGuess: "",
      log: [],
      computerMoves: [],
      userMoves: [],
      gameTime: 0,
      guessIndex: 100,
      computerThrowCount: 100,
      count: 0,
      highScoreRounds: -1,
      highScoreTime: -1,
      timeouts: [],
      gameStarted: false
    }
  },
  methods: {
      startNew: function(){

          // show green completion bars
          this.gameStarted = true;

          // reset variables
          this.guessIndex = 0;
          this.computerThrowCount = 0;

          // user start time
          this.gameTime = new Date().getTime() / 1000;

          // hide start button and show buttons
          document.getElementById('startbtn').style.display = 'none';
          document.getElementById('gamebtns').style.display = 'block';

          //show div that displays computer generated choice
          document.getElementById('computerMoves').style.display = 'block';

          // generate computer throw
          this.generateThrow();

          //display throws
          this.getThrows();

      },
      generateThrow: function(){

          // generate random number for computer throw
          this.computerMoves.push(Math.floor((Math.random() * 3) + 1));

          // clear so user has to re-enter what computer generated
          this.userMoves = [];

          // add to total computer throws
          this.computerThrowCount++;

      },
      getThrows: function(){
          for(var i = 0; i < this.computerMoves.length; i++){

              // display throws at one second intervals
              this.timeouts.push(setTimeout(this.display, i*1000, this.computerMoves[i], i+1));
          }

      },
      display: function(computerThrow, throwNum){

          //display the computer's throws
          switch(computerThrow) {
              case 1:
                  document.getElementById('image').src='src/assets/rock.jpg';
                  break;
              case 2:
                  document.getElementById('image').src='src/assets/paper.jpg';
                  break;
              case 3:
                  document.getElementById('image').src='src/assets/scissors.jpg';
                  break;
              default:
                  alert("there was an error with the computer generated throw");
          }

          this.count = throwNum;

      },
      compareThrows: function(){

          this.guessIndex++;

          // compare user choices to computer choices
          for(var i = 0; i < this.userMoves.length; i++){

              if(this.userMoves[i] == 1){

                  if(this.computerMoves[i] == 3){
                      //user selected correct option
                      if(this.userMoves.length == this.computerMoves.length){
                          this.timeouts.push(setTimeout(this.resetGuessIndex, 1000));
                          return;
                      }

                  }else{
                      //user selected wrong option
                      this.gameTime = (new Date().getTime() / 1000) - this.gameTime;
                      //alert("wrong User Move: " + this.userMoves[this.userMoves.length-1] + " Computer Move: " + this.computerMoves[this.computerMoves.length-1]);
                      this.updateLog(2);
                      this.checkHighScore();
                      this.guessIndex = 0;
                      return;
                  }
              }else if(this.userMoves[i] == 2){
                  if(this.computerMoves[i] == 1){
                      //user selected correct option
                      if(this.userMoves.length == this.computerMoves.length){
                          this.timeouts.push(setTimeout(this.resetGuessIndex, 1000));
                          return;
                      }

                  }else{
                      //user selected wrong option
                      this.gameTime = (new Date().getTime() / 1000) - this.gameTime;
                      //alert("wrong User Move: " + this.userMoves[this.userMoves.length-1] + " Computer Move: " + this.computerMoves[this.computerMoves.length-1]);
                      this.updateLog(2);
                      this.checkHighScore();
                      this.guessIndex = 0;
                      return;
                  }
              }else{
                  if(this.computerMoves[i] == 2){
                      //user selected correct option
                      if(this.userMoves.length == this.computerMoves.length){
                          this.timeouts.push(setTimeout(this.resetGuessIndex, 1000));
                          return;
                      }

                  }else{
                      //user selected wrong option
                      this.gameTime = (new Date().getTime() / 1000) - this.gameTime;
                      //alert("wrong User Move: " + this.userMoves[this.userMoves.length-1] + " Comupter Move: " + this.computerMoves[this.computerMoves.length-1]);
                      this.updateLog(2);
                      this.checkHighScore();
                      this.guessIndex = 0;
                      return;
                  }
              }

          }

      },
      resetGuessIndex: function(){
          this.guessIndex = 0;
          this.generateThrow();
          this.getThrows();
          this.updateLog(1);
      },
      updateLog: function(x){

          if(!this.gameStarted){

              this.log.unshift({
                  msg:"GAME IS OVER | PRESS RESTART TO PLAY AGAIN",
                  playerwin: false
              });

          }else if(x == 1){
              this.log.unshift({
                  msg:"COMPLETED ROUND " + (this.computerMoves.length -1),
                  playerwin: true
              });
          }else{

              this.gameStarted = false;

              this.log.unshift({
                  msg:"GAME OVER | ROUNDS COMPLETED " + (this.computerThrowCount - 1),
                  playerwin: false
              });
          }

      },
      rock: function(){

          if(!this.gameStarted){
              return;
          }

          // record user choice
          this.userMoves.push(1);

          this.compareThrows();

      },
      paper: function(){

          if(!this.gameStarted){
              return;
          }

          // record user choice
          this.userMoves.push(2);

          this.compareThrows();

      },
      scissors: function(){

          if(!this.gameStarted){
              return;
          }

          // record user choice
          this.userMoves.push(3);

          this.compareThrows();

      },
      restart: function(){

          // disable timeouts
          for (var i = 0; i < this.timeouts.length; i++) {
              clearTimeout(this.timeouts[i]);
          }
          // clear list
          this.timeouts = [];

          // calculate end time
          this.gameTime = new Date().getTime() / 1000;

          // reset variables
          this.computerWins = 0;
          this.userWins = 0;
          this.computerMoves = [];
          this.userMoves = [];
          this.gameTime = 0;
          this.userGuess = 0;
          this.count = 0;
          this.log = [];
          this.computerThrowCount = 0;
          this.gameStarted = false;

          // generate computer throw
          //this.generateThrow();

          //display throws
          //this.getThrows();

          document.getElementById('startbtn').style.display = 'block';
          document.getElementById('gamebtns').style.display = 'none';

          // hide computer generated throws div
          document.getElementById('computerMoves').style.display = 'none';
      },
      checkHighScore: function(){

          //dont record high scores if they don't pass the first round
          if(this.computerThrowCount == 1){
              return;
          }

          //alert("high score was checked | current/record: " + this.computerThrowCount + "/" + this.highScoreRounds + " | time " + this.highScoreTime);

          if(this.highScoreRounds == -1){
              this.highScoreRounds = this.computerThrowCount - 1;
              this.highScoreTime = this.gameTime;

              //alert("first hs");

              // add to log
              this.log.unshift({
                  msg:"NEW HIGH SCORE | ROUNDS " + (this.highScoreRounds - 1) + " | TIME " + this.highScoreTime.toFixed(2),
                  playertie: true
              });

          } else if(this.highScoreRounds < this.computerThrowCount){
              this.highScoreRounds = this.computerThrowCount - 1;
              this.highScoreTime = this.gameTime;

              //alert("more rounds");

              // add to log
              this.log.unshift({
                  msg:"NEW HIGH SCORE | ROUNDS " + (this.highScoreRounds - 1) + " | TIME " + this.highScoreTime.toFixed(2),
                  playertie: true
              });

          } else if(this.highScoreRounds == this.computerThrowCount){
              if(this.gameTime < this.highScoreTime){
                  this.highScoreRounds = this.computerThrowCount - 1;
                  this.highScoreTime = this.gameTime;

                  //alert("same rounds faster time");

                  // add to log
                  this.log.unshift({
                      msg:"NEW HIGH SCORE | ROUNDS " + (this.highScoreRounds - 1) + " | TIME " + this.highScoreTime.toFixed(2),
                      playertie: true
                  });

              }
          }
      }

  }
}
</script>

<style>
  .text-center {
    text-align: center;
  }

  .wins {
    width: 80%;
    color: black;
    height: 40px;
    background-color: #eee;
    margin: auto;
    transition: width 1000ms;
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
    color: green;
    background-color: #aaffb0;
  }

  .log ul .computer-turn {
    color: red;
    background-color: #ffc0c1;
  }

  .log ul .tie-turn {
    color: blue;
    background-color: #e4e8ff;
  }
</style>
