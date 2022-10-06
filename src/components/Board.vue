<template>
  <div>
    <h1 class="text-2xl my-9">Board of Users</h1>
    <div class="inline-block min-w-full shadow-md rounded-lg overflow-hidden mb-5">
      <table v-if="userList.length > 0" class="min-w-full leading-normal">
        <thead>
        <tr>
          <th class="px-5 py-3 border-b-2 border-gray-200 bg-gray-100 text-center text-xs font-semibold text-gray-700 uppercase tracking-wider">
            Name
          </th>
          <th class="px-5 py-3 border-b-2 border-gray-200 bg-gray-100 text-center text-xs font-semibold text-gray-700 uppercase tracking-wider">
            Total
          </th>
          <th class="px-5 py-3 border-b-2 border-gray-200 bg-gray-100 text-center text-xs font-semibold text-gray-700 uppercase tracking-wider">
            Badges
          </th>
        </tr>
        </thead>
        <tbody class="bg-white dark:bg-slate-800">
        <tr v-for="user in userList">
          <td class="px-5 py-5 border-b border-gray-200 bg-white text-sm">
            <p class="text-gray-900 whitespace-no-wrap">{{ user.name }}</p>
          </td>
          <td class="px-5 py-5 border-b border-gray-200 bg-white text-sm">{{ user.total }}</td>
          <td class="px-5 py-5 border-b border-gray-200 bg-white text-sm">
            <div v-for="badge in user.badges">
              <span>{{ badge }}</span>
            </div>
          </td>
        </tr>
        </tbody>
      </table>
    </div>
  </div>
</template>

<script>
export default {
  name: "Board",
  data() {
    return {
      userList: [],
    }
  },

  mounted() {
    this.getBoardList();
    window.setInterval(() => {
      this.getBoardList();
    }, 5);
  },

  methods: {
    getBoardList() {
      fetch(process.env.API_BACKEND + '/boards').then(res => {
        if (res.ok) {
          res.json().then(response => {
            this.getUserList().then(users => {
              let userMap = new Map();
              users.forEach(user => {
                userMap.set(user.id, user.name);
              });

              response.forEach(row => {
                row['name'] = userMap.get(row.userId);
              });

              this.userList = response;
            });
          });
        }
      })
    },

    getUserList() {
      return fetch(process.env.API_BACKEND + '/users').then(users => {
        if (users.ok) {
          return users.json();
        } else {
          return Promise.reject("Error response from the backend.");
        }
      });
    }
  }
}
</script>

<style scoped>

</style>
