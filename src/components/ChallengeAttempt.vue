<template>
  <div>
    <div class="mb-10">
      <p>Player: {{ name }}</p>
    </div>
    <div class="inline-block min-w-full shadow-md rounded-lg overflow-hidden mb-5">
      <table v-if="userAttempts.length > 0" class="min-w-full leading-normal">
        <thead>
        <tr>
          <th class="px-5 py-3 border-b-2 border-gray-200 bg-gray-100 text-center text-xs font-semibold text-gray-700 uppercase tracking-wider">
            Word
          </th>
          <th class="px-5 py-3 border-b-2 border-gray-200 bg-gray-100 text-center text-xs font-semibold text-gray-700 uppercase tracking-wider">
            Attempt Left
          </th>
          <th class="px-5 py-3 border-b-2 border-gray-200 bg-gray-100 text-center text-xs font-semibold text-gray-700 uppercase tracking-wider">
            Success
          </th>
        </tr>
        </thead>
        <tbody class="bg-white dark:bg-slate-800">
        <tr v-for="item in userAttempts">
          <td class="px-5 py-5 border-b border-gray-200 bg-white text-sm">
            <p class="text-gray-900 whitespace-no-wrap">{{ item.word }}</p>
          </td>
          <td class="px-5 py-5 border-b border-gray-200 bg-white text-sm">{{ item.maxAttempt }}</td>
          <td class="px-5 py-5 border-b border-gray-200 bg-white text-sm">
            <div v-if="item.success === true">
              <CheckCircleIcon class="text-green-500 m-auto w-100"/>
            </div>
            <div v-else>
              <XCircleIcon class="text-red-500 m-auto w-100" />
            </div>
          </td>
        </tr>
        </tbody>
      </table>
    </div>
    <button @click="restartGame" class="rounded-lg px-4 py-2 bg-blue-500 text-blue-100 hover:bg-blue-600 duration-300">Restart Game</button>
  </div>
</template>

<script>
import { CheckCircleIcon } from "@vue-hero-icons/outline"
import { XCircleIcon } from "@vue-hero-icons/outline"
export default {
  name: "ChallengeAttempt",
  props: ['name', 'word', 'maxAttempt', 'success'],
  components: {
    CheckCircleIcon,
    XCircleIcon
  },
  data () {
    return {
      userAttempts: []
    }
  },

  mounted() {
    if (this.name) {
      this.sendAttempt().then(res => {
        if (res.ok) {
          console.log(`Res: ${res}`)
          this.getUserAttempts(this.name);
        } else {
          console.log('Error on the server')
        }
      });
    }
  },

  computed: {
    status() {
      return this.success === true ? 'won' : 'loose';
    }
  },
  methods: {
    sendAttempt() {
      return fetch(process.env.API_BACKEND + '/challenge-attempt', {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json'
        },
        body: JSON.stringify({
          name: this.name,
          word: this.word,
          maxAttempt: this.maxAttempt,
          success: this.success
        })
      });
    },

    getUserAttempts(name) {
      fetch(process.env.API_BACKEND + '/challenge-attempt?name=' + name).then(res => {
        if (res.ok) {
          res.json().then(data => {
            data.forEach(item => {
              this.userAttempts.push(item);
            })
          })
        }
      })
    },

    restartGame() {
      this.$parent.refreshGame();
      this.$parent.getWordChallenge();
    }
  }
}
</script>

<style scoped>

</style>
