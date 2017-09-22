<template>
    <div class="container">
        <div>
            <h3 v-if="!showLoadButton" class="text-center">Well well traveller, seems you have strayed too far from home. You're in Bolton territory now. Tell you what, I'll let you go..</h3>
            <div class="text-center">
                <p v-if="!showLoadButton" @click="walkAway = true; showLoadButton = true;" class="initial-choice">Boltons are mad, lets get the hell out of here! (Walk away)</p>
                <p v-if="!showLoadButton" @click="standGround = true; showLoadButton = true;" class="initial-choice">Pfft screw House Bolton, what's one soldier gonna do? (Stand your ground)</p>
            </div>
            <transition name="slide-fade">
                <h3 v-if="walkAway" class="text-center">On one condition.</h3>
            </transition>
            <transition name="slide-fade">
                <h3 v-if="standGround" class="text-center">Oh I see we've got a brave one, let's see what you're made of.</h3>
            </transition>
        </div>
        <div class="text-center">
            <p v-if="showLoadButton" @click="loadTrivia" class="next">Next</p>
        </div>
    </div>
</template>

<script>
import { eventBus } from '../main'
export default {
  data() {
          return {
              walkAway: false,
              standGround: false,
              showLoadButton: false
          }
      },
      methods: {
          // Tell parent component to render trivia component
          loadTrivia() {
              eventBus.$emit('loadedTrivia')
          }
      }
}
</script>

<style scoped>

/* General */
.container {
    margin-top: 50px;
}
h3 {
    color: black;
    font-family: 'Shadows Into Light', cursive;
    font-size: 30px;
}

/* Buttons */
.next {
    font-family: 'Reenie Beanie', cursive;
    font-size: 25px;
    background-color: rgb(219, 231, 243);
    color: black;
    border: 1px solid black;
    width: 50%;
    margin: 0 auto;
    margin-bottom: 5px;
}
.next:hover {
    background-color: rgb(255, 64, 69);
}
.initial-choice {
    font-family: 'Reenie Beanie', cursive;
    font-size: 30px;
    background-color: rgb(219, 231, 243);
    color: black;
    border: 1px solid black;
}
.initial-choice:hover {
    background-color: rgb(255, 64, 69);
}
.btn {
    font-family: 'Reenie Beanie', cursive;
    font-size: 20px;
    color: black;
    background-color: rgb(219, 231, 243);
    border: 1px solid grey;
    width: 60%;
    cursor: pointer;
}

</style>
