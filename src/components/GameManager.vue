<template>
    <div class="flex flex-col justify-center items-center">

      <div v-if="!endGame && !winner">
        <h1 class="text-grey-700 text-3xl mb-5">Guess the word</h1>
        <div class="flex">
          <span class="text-m mr-2">Name: </span>
          <input v-model="name"
                 type="text"
                 class="text-xl text-gray-base w-full mr-3 py-5 px-4 h-2 border
                                border-gray-200 rounded mb-2 ">
        </div>

        <div class="mb-3 text-3xl">
          {{ hiddenWord }}
        </div>
        <h3 class="mb-2">You have {{ maxAttempt }} attempt(s) left.</h3>
        <input
          v-model="guess"
          @keyup.enter="onEnter"
          maxlength="1"
          class="text-xl text-gray-base w-full mr-3 py-5 px-4 h-2 border
                                border-gray-200 rounded mb-2"
        >
        <div class="incorrect-chars">
          <p>Wrong letters: {{ displayIncorrectChars }}</p>
        </div>
      </div>
      <div v-else-if="winner">
        <h1 class="text-5xl mb-5">You won!</h1>
        <ChallengeAttempt
          :name="name"
          :word="word"
          :max-attempt="maxAttempt"
          :success="true"
        />
        <!-- display the rating -->
      </div>
      <div v-else>
        <h1 class="text-3xl mb-5">Game Over!</h1>
        <p class="text-lg">The correct word is: <span class="text-2xl">{{ word }}</span></p>
        <ChallengeAttempt
          :name="name"
          :word="word"
          :max-attempt="maxAttempt"
          :success="false"
        />
      </div>
  </div>
</template>

<script>
import ChallengeAttempt from "./ChallengeAttempt";
export default {
  name: "GameManager",
  components: {ChallengeAttempt},
  data () {
    return {
      name: '',
      word: '',
      guess: '',
      charList: [],
      incorrectChars: [],
      maxAttempt: 5,
    }
  },

  mounted() {
    this.getWordChallenge();
  },

  computed: {
    hiddenWord() {
      console.log(`Word: ${this.word}`)
      return this.word.split('').map(char => {
        if (this.charList.includes(char)) {
          return char;
        } else {
          return '_';
        }
      }).join(' ');
    },

    endGame() {
      return this.maxAttempt === 0;
    },

    displayIncorrectChars() {
      return this.incorrectChars.join(',');
    },

    winner() {
      return this.word === this.hiddenWord.replace(/\s/g, '');
    }
  },

  methods: {
    getWordChallenge() {
      fetch(process.env.API_BACKEND + '/challenge/word').then(res => {
        if (res.ok) {
          res.json().then(resp => {
            this.word = resp.word;
            console.log(`Res: ${resp.word}`)
          })
        }
      })
    },


    onEnter() {
      const charGuess = this.guess.toLocaleLowerCase();
      this.charList.push(charGuess);
      if (!this.word.includes(charGuess)) {
        this.incorrectChars.push(charGuess);
        this.maxAttempt--;
      }
      this.guess = '';
    }
  }
}
</script>

<style scoped>

</style>
