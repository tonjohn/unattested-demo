<template>
  <div class="container">
    <div>
      <Logo />
      <h1 class="title">unattested-demo</h1>
      <section id="debug">
        <ul>
          <li>Device ID: {{ device_id }}</li>
          <li>Recovery Code: {{ recovery_code }}</li>
        </ul>
        <section class="pt-5">
          <h3 class="text-lg leading-6 font-medium text-gray-900">
            Refresh Token
          </h3>
          <textarea>{{ refresh_token }}</textarea>
        </section>
        <section class="pt-5">
          <h3 class="text-lg leading-6 font-medium text-gray-900">
            Access Token
          </h3>
          <textarea>{{ access_token }}</textarea>
        </section>
      </section>
      <section id="register">
        <div
          class="min-h-screen bg-gray-50 flex flex-col justify-center py-12 sm:px-6 lg:px-8"
        >
          <div class="sm:mx-auto sm:w-full sm:max-w-md">
            <img
              class="mx-auto h-12 w-auto"
              src="https://tailwindui.com/img/logos/workflow-mark-indigo-600.svg"
              alt="Workflow"
            />
            <h2 class="mt-6 text-center text-3xl font-extrabold text-gray-900">
              Create your VetBabble account
            </h2>
          </div>

          <div class="mt-8 sm:mx-auto sm:w-full sm:max-w-md">
            <div class="bg-white py-8 px-4 shadow sm:rounded-lg sm:px-10">
              <form
                @submit.prevent="registerUser"
                class="space-y-6"
                action="#"
                method="POST"
              >
                <div>
                  <label
                    for="username"
                    class="block text-sm font-medium text-gray-700"
                  >
                    Username
                  </label>
                  <div class="mt-1">
                    <input
                      v-model="accountForm.username"
                      id="username"
                      name="username"
                      type="text"
                      autocomplete="username"
                      required
                      class="appearance-none block w-full px-3 py-2 border border-gray-300 rounded-md shadow-sm placeholder-gray-400 focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 sm:text-sm"
                    />
                  </div>
                </div>
                <div>
                  <label
                    for="email"
                    class="block text-sm font-medium text-gray-700"
                  >
                    Email address
                  </label>
                  <div class="mt-1">
                    <input
                      v-model="accountForm.email"
                      id="email"
                      name="email"
                      type="email"
                      autocomplete="email"
                      required
                      class="appearance-none block w-full px-3 py-2 border border-gray-300 rounded-md shadow-sm placeholder-gray-400 focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 sm:text-sm"
                    />
                  </div>
                </div>

                <div>
                  <label
                    for="password"
                    class="block text-sm font-medium text-gray-700"
                  >
                    Password
                  </label>
                  <div class="mt-1">
                    <input
                      v-model="accountForm.password"
                      id="password"
                      name="password"
                      type="password"
                      autocomplete="new-password"
                      required
                      class="appearance-none block w-full px-3 py-2 border border-gray-300 rounded-md shadow-sm placeholder-gray-400 focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 sm:text-sm"
                    />
                  </div>
                </div>

                <div>
                  <label
                    for="password"
                    class="block text-sm font-medium text-gray-700"
                  >
                    Confirm Password
                  </label>
                  <div class="mt-1">
                    <input
                      v-model="accountForm.password2"
                      id="password2"
                      name="password2"
                      type="password"
                      autocomplete="new-password"
                      required
                      class="appearance-none block w-full px-3 py-2 border border-gray-300 rounded-md shadow-sm placeholder-gray-400 focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 sm:text-sm"
                    />
                  </div>
                </div>

                <div>
                  <button
                    type="submit"
                    class="w-full flex justify-center py-2 px-4 border border-transparent rounded-md shadow-sm text-sm font-medium text-white bg-indigo-600 hover:bg-indigo-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500"
                  >
                    Create Account
                  </button>
                </div>
              </form>
            </div>
          </div>
        </div>
      </section>
      <div class="links">
        <a
          href="https://nuxtjs.org/"
          target="_blank"
          rel="noopener noreferrer"
          class="button--green"
        >
          Documentation
        </a>
        <a
          href="https://github.com/nuxt/nuxt.js"
          target="_blank"
          rel="noopener noreferrer"
          class="button--grey"
        >
          GitHub
        </a>
      </div>
    </div>
  </div>
</template>

<script>
/**
 * @typedef UnattestedCreateAccountResponse
 * @type {Object}
 * @property {string} refresh
 * @property {string} recovery_code
 */

/**
 * @typedef RefreshTokenResponse
 * @type {Object}
 * @property {string} access
 */

/**
 * @typedef UnattestedRegistrationResponse
 * @type {Object}
 * @property {string} refresh
 */
function uuidv4() {
  return 'xxxxxxxx-xxxx-4xxx-yxxx-xxxxxxxxxxxx'.replace(/[xy]/g, function (c) {
    var r = (Math.random() * 16) | 0,
      v = c === 'x' ? r : (r & 0x3) | 0x8
    return v.toString(16)
  })
}

const BASE_URL = 'http://127.0.0.1:8000'
const UNATTEST_LOGIN_URL = `${BASE_URL}/api/jwtauth/register/unattested/`
const URL_API_TOKEN_REFRESH = `${BASE_URL}/api/token/refresh/`
const URL_API_USER_REGISTRATION = `${BASE_URL}/api/jwtauth/register/`

export default {
  components: {},
  data: function () {
    return {
      device_id: '',
      access_token: '',
      refresh_token: '',
      recovery_code: '',
      accountForm: {
        username: '',
        email: '',
        password: '',
        password2: '',
      },
    }
  },
  methods: {
    async unattestedLogin() {
      const reqBody = {}

      const reqOptions = {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json',
          'DEVICE-ID': this.device_id,
        },
        cache: 'no-cache',
        credentials: 'include',
        body: JSON.stringify(reqBody),
      }

      let response = await fetch(UNATTEST_LOGIN_URL, reqOptions)
        .then((resLogin) => resLogin.json()) // see todo
        .catch((err) => {
          console.log({ err })
          return null
        }) // @type {UnattestedCreateAccountResponse}

      if (response) {
        console.log({ response })
        this.recovery_code = response.recovery_code
        this.refresh_token = response.refresh
        this.fetchAccessToken()
      }
    },
    async fetchAccessToken() {
      const reqBody = {
        refresh: this.refresh_token,
      }

      const reqOptions = {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json',
          'DEVICE-ID': this.device_id,
        },
        cache: 'no-cache',
        credentials: 'include',
        body: JSON.stringify(reqBody),
      }

      return await fetch(URL_API_TOKEN_REFRESH, reqOptions)
        .then((resLogin) => resLogin.json())
        .then((data) => {
          console.log('REFRESHING TOKEN')
          console.log({ data })
          this.access_token = data.access
          return true
        })
        .catch((err) => {
          console.log({ err })
          return false
        })
    },
    async registerUser() {
      const reqBody = {
        username: this.accountForm.username,
        email: this.accountForm.email,
        password: this.accountForm.password,
        password2: this.accountForm.password2,
      }

      const reqOptions = {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json',
          'DEVICE-ID': this.device_id,
          Authorization: `Bearer ${this.access_token}`,
        },
        cache: 'no-cache',
        credentials: 'include',
        body: JSON.stringify(reqBody),
      }

      return await fetch(URL_API_USER_REGISTRATION, reqOptions)
        .then((resLogin) => resLogin.json())
        .then((data) => {
          console.log('USER REGISTERED')
          console.log({ data })
          this.userInfo = data
          return true
        })
        .catch((err) => {
          console.log({ err })
          return false
        })
    },
  },
  created: function () {
    this.device_id = uuidv4()
    this.unattestedLogin()
  },
}
</script>

<style>
/* Sample `apply` at-rules with Tailwind CSS
.container {
@apply min-h-screen flex justify-center items-center text-center mx-auto;
}
*/
.container {
  margin: 0 auto;
  min-height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  text-align: center;
}

.title {
  font-family: 'Quicksand', 'Source Sans Pro', -apple-system, BlinkMacSystemFont,
    'Segoe UI', Roboto, 'Helvetica Neue', Arial, sans-serif;
  display: block;
  font-weight: 300;
  font-size: 100px;
  color: #35495e;
  letter-spacing: 1px;
}

.subtitle {
  font-weight: 300;
  font-size: 42px;
  color: #526488;
  word-spacing: 5px;
  padding-bottom: 15px;
}

.links {
  padding-top: 15px;
}
</style>
