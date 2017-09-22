<template>
    <div class="container">
        <div v-if="show">
            <h2 class="text-center question">{{questions[questionIndex].question}}</h2>
            <div v-if="!submittedAnswer" class="row text-center" id="questions-container">
                <div v-for="(item,index) in questions[questionIndex].choices" class="col-sm-6 text-center choice">
                    <p class="shadow-effect" :class="{'active': activeChoiceId == index}" :id="index" @click="chooseAnswer">{{item}}</p>
                </div>
            </div>
            <div v-if="submittedAnswer" class="text-center">
                <h2 class="correct-answer"> {{questions[questionIndex].answer}}</h2>
            </div>
            <div class="text-center button-container">
                <p v-if="!submittedAnswer && !outOfTime" @click="submitAnswer" class="btn">Submit answer</p>
                <p v-if="submittedAnswer" @click="nextQuestion" class="btn">Next question</p>
                <p v-if="outOfTime" @click="submitAnswer" class="btn">Next</p>
            </div>
        </div>
        <div class="text-center" v-else>
            <p class="instruction">The Bolton bastards have made a proposal, survive by answering as many questions correct until they've got nothing left to throw at you. May the trivia gods be in your favor.</p>
            <p class="start" @click="initialize">Start</p>
        </div>
    </div>
</template>

<script>
  import { eventBus } from '../main'
  export default {
      props: ['questions', 'timeRanOut'],
      data() {
          return {
              correct: 0,
              incorrect: 0,
              questionIndex: 0,
              answer: '',
              submittedAnswer: false,
              activeChoiceId: null,
              show: false,
              outOfTime: null,
          }
      },
      methods: {
          initialize() {
                  let vm = this;
                  vm.show = true;
                  eventBus.$emit('startTimer');
                  vm.imgUrl = vm.questions[vm.questionIndex].imgUrl;
              },
              nextQuestion() {
                  let vm = this;
                  vm.activeChoiceId = null;
                  if (vm.questionIndex === vm.questions.length - 1) {
                      eventBus.$emit('finishedQuestions');
                  } else {
                      vm.questionIndex++;
                      vm.submittedAnswer = !vm.submittedAnswer;
                      eventBus.$emit('startTimer', vm.submittedAnswer);
                  }
              },
              submitAnswer() {
                  let vm = this;
                  let userAnswer = vm.answer.toLowerCase();
                  let realAnswer = vm.questions[vm.questionIndex].answer.toLowerCase();
                  userAnswer === realAnswer ? vm.correct++ : vm.incorrect++;
                  eventBus.$emit('updateIncorrectCorrect', {
                      incorrect: vm.incorrect,
                      correct: vm.correct
                  });
                  vm.answer = ''
                  vm.submittedAnswer = !vm.submittedAnswer;
                  eventBus.$emit('startTimer', vm.submittedAnswer);
              },
              chooseAnswer() {
                  let vm = this;
                  vm.answer = event.currentTarget.innerHTML.trim();
                  vm.activeChoiceId = event.currentTarget.id;
              }
      },
      watch: {
          timeRanOut() {
              let vm = this;
              vm.outOfTime = vm.timeRanOut;
          }
      }
  }
</script>

<style scoped>
@font-face {
    font-family: hero;
    src: url(../../static/Hero.otf);
}
@font-face {
    font-family: herolight;
    src: url(../../static/HeroLight.otf);
}

/* General */
.container {
    margin-top: 10px;
    padding-top: 10px;
}
.instruction {
    font-family: 'Shadows Into Light', cursive;
    color: black;
    font-size: 30px;
}
.question {
    margin-top: 5px;
    margin-bottom: 20px;
    font-family: 'Shadows Into Light', cursive;
    color: black;
    font-size: 30px;
}
.correct-answer {
    font-family: 'Shadows Into Light', cursive;
    color: rgb(194, 178, 128);
    font-size: 40px;
    font-weight: bold;
}

/* Buttons */
.button-container {
    margin-bottom: 5px;
}
.btn {
    font-family: 'Reenie Beanie', cursive;
    font-size: 20px;
    color: white;
    background-color: black;
    border: 1px solid grey;
    width: 60%;
}
.start {
    font-family: 'Reenie Beanie', cursive;
    font-size: 25px;
    background-color: rgb(219, 231, 243);
    color: black;
    border: 1px solid black;
    width: 50%;
    margin: 0 auto;
    margin-bottom: 5px;
}
.start:hover {
    background-color: rgb(53, 191, 64);
}
.active {
    background-color: rgb(123, 167, 210)!important;
    color: white!important;
}
.choice p {
    font-family: herolight, cursive;
    font-size: 24.5px;
    color: black;
    background-color: rgb(219, 231, 243);
    cursor: pointer;
}

/* Effects */
.shadow-effect {
    position: relative;
    -webkit-box-shadow: 0 1px 2px rgba(0, 0, 0, 0.3), 0 0 40px rgba(0, 0, 0, 0.1) inset;
    -moz-box-shadow: 0 1px 2px rgba(0, 0, 0, 0.3), 0 0 40px rgba(0, 0, 0, 0.1) inset;
    box-shadow: 0 1px 2px rgba(0, 0, 0, 0.3), 0 0 40px rgba(0, 0, 0, 0.1) inset;
}
.shadow-effect:before,
.shadow-effect:after {
    content: "";
    position: absolute;
    z-index: -1;
    -webkit-box-shadow: 0 0 10px rgba(0, 0, 0, 0.8);
    -moz-box-shadow: 0 0 10px rgba(0, 0, 0, 0.8);
    box-shadow: 0 0 10px rgba(0, 0, 0, 0.8);
    top: 0;
    bottom: 0;
    left: 10px;
    right: 10px;
    -moz-border-radius: 100px / 10px;
    border-radius: 100px / 10px;
}
.shadow-effect:after {
    right: 10px;
    left: auto;
    -webkit-transform: skew(8deg) rotate(3deg);
    -moz-transform: skew(8deg) rotate(3deg);
    -ms-transform: skew(8deg) rotate(3deg);
    -o-transform: skew(8deg) rotate(3deg);
    transform: skew(8deg) rotate(3deg);
}

/* Media Queries */
@media (min-width: 1024px) {
    .choice p {
        margin-bottom: 20px;
    }
}
</style>
