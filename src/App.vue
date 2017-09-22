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
import Trivia from './components/Trivia.vue'
import Intro from './components/Intro.vue'
import ProgressBar from './components/ProgressBar.vue'
import Timer from './components/Timer.vue'
import { eventBus } from './main'

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
                questions: [    {
                    question: 'How many times is Jon stabbed when he is murdered at Castle Black?',
                    choices: ['1', '6', '9', '10'],
                    answer: '6'
                }, {
                    question: 'What name do the wildlings call Jon Snow?',
                    choices: ['Jon', 'Big Boi', 'Puny', 'King Crow'],
                    answer: 'King Crow'
                }, {
                    question: 'What does Sansa place in the hand of Lyanna Stark’s statue?',
                    choices: ['A feather', 'A necklace', 'An earring', 'Snow'],
                    answer: 'A feather'
                }, {
                    question: 'What\'s the name of the explosive that gave the Lannisters the edge in the Battle of Blackwater?',
                    choices: ['Dragonfire', 'Godsfire', 'Wildfire', 'Wildseed'],
                    answer: 'Wildfire'
                }, {
                    question: 'Who said, "Go drink until it feels like you did the right thing"?',
                    choices: ['Jamie Lannister', 'Tyrion Lannister', 'Bronn', 'Varys'],
                    answer: 'Bronn'
                },{
                    question: 'What does Daenerys mean when she says "Shekh ma shieraki anni" to Khal Drogo?',
                    choices: ['Sound did silence me', 'Moon of my life', 'My sun and stars', 'I am yours and you are mine'],
                    answer: 'My sun and stars'
                },{
                    question: 'How is Ser Davos colloquially known as?',
                    choices: ['The Sea Knight', 'Davos the Smuggler', 'The Cabbage Duke', 'The Onion Knight'],
                    answer: 'The Onion Knight'
                }, {
                    question: 'Who masterminded the plot to kill King Joffrey?',
                    choices: ['Littlefinger', 'Olenna Tyrell', 'Margaery Tyrell', 'Arya Stark'],
                    answer: 'Littlefinger'
                }, {
                    question: 'What is the official Lannister family motto?',
                    choices: ['Hear me roar', 'A Lannister always pays his debts', 'None so fierce', 'Never knowingly undersold'],
                    answer: 'Hear Me Roar'
                },{
                    question: 'What name does Littlefinger use to disguise Sansa when he brings her to the Eyrie?',
                    choices: ['Susan', 'Alayne', 'Margarette', 'Elena'],
                    answer: 'Alayne'
                }, {
                    question: 'What are Rickon’s last words to Bran?',
                    choices: ['Look after Hodor', 'I don\'t want to leave you', 'When will I see you again?', 'Winter is coming'],
                    answer: 'I don\'t want to leave you'
                }, {
                    question: 'According to Jon, how many times has a King Beyond the Wall attacked the Seven Kingdoms?',
                    choices: ['6', '0', '2', '10'],
                    answer: '6',
                }, {
                    question: '"When you play the Game of Thrones, you win or you die." Cersei Lannister first uttered these words to who?',
                    choices: ['Varys', 'Ned Stark', 'Robert Baratheon', 'Margaery Tyrell'],
                    answer: 'Ned Stark',
                }, {
                    question: 'Who helped Daenerys and her followers get into the city of Qarth?',
                    choices: ['The Spice King', 'She didn\'t get in', 'Jorah Mormont', 'Xaro Xhoan Daxos'],
                    answer: 'Xaro Xhoan Daxos',
                }, {
                    question: 'Where did Tywin and the Lannister army set up camp during the Ware of the Five Kings?',
                    choices: ['Casterly Rock', 'Riverrun', 'The Twins', 'Harrenhal'],
                    answer: 'Harrenhal',
                }, {
                    question: 'How was Renly Baratheon killed?',
                    choices: ['Stabbed by a shadow', 'In his sleep', 'Poisoned', 'In battle'],
                    answer: 'Stabbed by a shadow',
                }, {
                    question: 'Samwell of house...?',
                    choices: ['Tully', 'Greyjoy', 'Tarly', 'Frey'],
                    answer: 'Tarly',
                }, {
                    question: 'Barristan Selmy is slain by',
                    choices: ['Sons of the Harpy', 'The Kings Guard', 'A slave', 'City Watch'],
                    answer: 'Sons of the Harpy',
                }, {
                    question: 'Myrcella Baratheon is killed by who?',
                    choices: ['Ellaria Sand', 'Obara Sand', 'Tyene Sand', 'Nymeria Sand'],
                    answer: 'Ellaria Sand',
                }, {
                    question: 'What was the name of the first Targaryen king who conquered Westeros with his two sisters?',
                    choices: ['Aegon II Targaryen', 'Aegon IV Targaryen', 'Aegon I Targaryen', 'Rhaegar Targaryen'],
                    answer: 'Aegon I Targaryen',
                },{
                    question: 'What do the wildings call themselves?',
                    choices: ['Folk of the north', 'Travellers', 'Free Folk', 'Crows'],
                    answer: 'Free Folk',
                }, {
                    question: 'What did Rickon Stark name his direwolf?',
                    choices: ['Summer', 'Grey Wind', 'Nymeria', 'Shaggydog'],
                    answer: 'Shaggydog',
                }]
            }
        },
        methods: {
            printResult() {
                let playerHP = this.playerHP;
                if (playerHP <= 0) {
                    return 'I mean, it\'s Westeros, you never stood a chance.'
                } else if (playerHP >= 1 && playerHP <= 30) {
                    return 'Fortunate, you just managed to scrape by. Count your blessings.'
                } else if (playerHP > 30 && playerHP <= 50) {
                    return 'You came out with minor injuries, next time you may not be so lucky.'
                } else if (playerHP > 50 && playerHP <= 70) {
                    return 'I must say, you faired pretty well. Ever thought about taking the black?'
                } else if (playerHP > 70 && playerHP <= 100) {
                    return 'Seven hells! The gods clearly favor you.'
                } else {
                    return
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
            eventBus.$on('updateIncorrectCorrect', (data) => {
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
            eventBus.$on('startTimer', (data) => {
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
                }, 500)
            });
        }
}
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
    transition: all .6s ease;
}
.slide-fade-leave-active {
    transition: all .4s cubic-bezier(1.0, 0.5, 0.8, 1.0);
}
.slide-fade-enter,
.slide-fade-leave-to
/* .slide-fade-leave-active below version 2.1.8 */

{
    transform: translateX(10px);
    opacity: 0;
}
</style>
