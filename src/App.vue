<template>
  <div id="app">
    <h1 class="text-center">Game of Trivia</h1>
    <ProgressBar v-if="show" :playerHP="playerHP"></ProgressBar>
    <!-- Will only show if user is not dead or has not answered all questions -->
    <Trivia :timeRanOut="timeRanOut" :questions="questions" v-if="show && !dead && !answeredAll"></Trivia>
    <Timer v-if="show" :timeLeft="timeLeft"></Timer>
    <Intro v-if="!show && !dead && !answeredAll"></Intro>
    <!-- render when player is dead -->
    <transition name="slide-fade">
      <div class="text-center results" v-if="dead">
        <h1 class="text-center">You are dead.</h1>
        <p class="stats">{{printResult()}}</p>
        <p class="stats">Correct: {{correct}}</p>
        <p class="stats">Incorrect: {{incorrect}}</p>
      </div>
    </transition>
    <!--render when player answered all without dieing -->
    <transition name="slide-fade">
      <div class="text-center results" v-if="answeredAll">
        <h1 class="text-center">You won</h1>
        <p class="stats">{{printResult()}}</p>
        <p class="stats">Correct: {{correct}}</p>
        <p class="stats">Incorrect: {{incorrect}}</p>
      </div>
    </transition>
  </div>
</template>

<script>
import Trivia from './components/Trivia.vue';
import Intro from './components/Intro.vue';
import ProgressBar from './components/ProgressBar.vue';
import Timer from './components/Timer.vue';
import { eventBus } from './main';
import questions from './data/questions';

export default {
  data() {
    return {
      show: false,
      timeLeft: 10,
      timeRanOut: false,
      playerHP: 100,
      dead: false,
      incorrect: 0,
      correct: 0,
      answerChosen: false,
      answeredAll: false,
      timeRanOut: false,
      questions
    };
  },
  methods: {
    printResult() {
      let playerHP = this.playerHP;
      if (playerHP <= 0) {
        return "I mean, it's Westeros, you never stood a chance.";
      } else if (playerHP >= 1 && playerHP <= 30) {
        return 'Fortunate, you just managed to scrape by. Count your blessings.';
      } else if (playerHP > 30 && playerHP <= 50) {
        return 'You came out with minor injuries, next time you may not be so lucky.';
      } else if (playerHP > 50 && playerHP <= 70) {
        return 'I must say, you faired pretty well. Ever thought about taking the black?';
      } else if (playerHP > 70 && playerHP <= 100) {
        return 'Seven hells! The gods clearly favor you.';
      } else {
        return;
      }
    }
  },
  components: {
    Trivia,
    Intro,
    ProgressBar,
    Timer
  },
  created() {
    // Render trivia component.
    eventBus.$on('loadedTrivia', () => {
      this.show = true;
    });
    // Update playerHP according to user incorrect count.
    eventBus.$on('updateIncorrectCorrect', data => {
      let vm = this;
      // calculate damage
      let damage = data.incorrect * Math.floor(Math.random() * 7) + 1;
      let currentIncorrect = vm.incorrect;
      vm.incorrect = data.incorrect;
      vm.correct = data.correct;
      // if damage is greater than current HP, user is dead.
      if (damage > vm.playerHP && vm.incorrect > currentIncorrect) {
        vm.playerHP = 0;
        vm.dead = true;
      }
      // user has answered a question incorrectly update HP which will then on update ProgressBar module.
      else if (vm.incorrect > currentIncorrect) {
        vm.playerHP -= damage;
      }
    });
    // user has succesfully answered all questions with HP > 0
    eventBus.$on('finishedQuestions', () => {
      this.answeredAll = true;
    });
    // initiate timer
    eventBus.$on('startTimer', data => {
      let vm = this;
      vm.answerChosen = data;
      vm.timeRanOut = false;
      var countdown = setInterval(() => {
        // user has chosen answer
        if (vm.answerChosen) {
          clearInterval(countdown);
          vm.timeLeft = 10;
          // user has not submitted an answer and ran out of time
        } else if (!vm.answerChosen && vm.timeLeft <= 0) {
          clearInterval(countdown);
          vm.timeRanOut = true;
          // user loses hp
          vm.playerHP -= 10;
          vm.timeRanOut = true;
        } else {
          vm.timeLeft -= 0.5;
        }
      }, 500);
    });
  }
};
</script>

<style>
@font-face {
  font-family: gameOfThrones;
  src: url(../static/got.ttf);
}
#app {
  margin-top: 30px;
}
.results {
  margin: 0 auto;
}
.stats {
  font-family: 'Reenie Beanie', cursive;
  font-size: 35px;
}
h1 {
  font-family: gameOfThrones, 'cursive';
  color: black;
}

/* Effects */
.slide-fade-enter-active {
  transition: all 0.6s ease;
}
.slide-fade-leave-active {
  transition: all 0.4s cubic-bezier(1, 0.5, 0.8, 1);
}
.slide-fade-enter,
.slide-fade-leave-to
/* .slide-fade-leave-active below version 2.1.8 */

 {
  transform: translateX(10px);
  opacity: 0;
}
</style>
