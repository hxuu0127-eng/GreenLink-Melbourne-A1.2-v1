```vue
<template>
  <div class="app-container">
    <header class="main-header">
      <h1>GreenLink Melbourne</h1>
      <p>Urban greening, tree planting and biodiversity</p>

      <div v-if="currentUser" class="user-bar">
        <span>
          Welcome, {{ currentUser.name }} ({{ currentUser.role }})
        </span>
        <button @click="logout">Logout</button>
      </div>
    </header>

    <main>
      <section v-if="!currentUser" class="auth-section">
        <h2>{{ authMode === 'login' ? 'Login' : 'Register' }}</h2>

        <form @submit.prevent="authMode === 'login' ? login() : registerAccount()">
          <input
            v-if="authMode === 'register'"
            v-model.trim="accountName"
            type="text"
            placeholder="Full Name"
            maxlength="50"
          >

          <input
            v-model.trim="accountEmail"
            type="email"
            placeholder="Email Address"
            maxlength="100"
            required
          >

          <input
            v-model="accountPassword"
            type="password"
            placeholder="Password"
            minlength="6"
            maxlength="100"
            required
          >

          <button type="submit">
            {{ authMode === 'login' ? 'Login' : 'Register' }}
          </button>
        </form>

        <button
          class="link-button"
          @click="authMode = authMode === 'login' ? 'register' : 'login'"
        >
          {{ authMode === 'login'
            ? 'Create an account'
            : 'Already have an account? Login' }}
        </button>

        <p v-if="message" :class="messageType">
          {{ message }}
        </p>
      </section>

      <section
        v-if="currentUser && currentUser.role === 'User'"
        class="user-interface"
      >
        <section class="intro-section">
          <h2>Welcome to GreenLink Melbourne</h2>
          <p>
            Explore local environmental activities and help create a greener
            Melbourne.
          </p>

          <div class="stats-bar">
            <div>
              <strong>300+</strong>
              <span>Trees Planted</span>
            </div>

            <div>
              <strong>{{ events.length }}</strong>
              <span>Activities</span>
            </div>

            <div>
              <strong>{{ totalPlaces }}</strong>
              <span>Open Spots</span>
            </div>
          </div>
        </section>

        <section class="events-section">
          <h2>Upcoming Activities</h2>

          <div class="events-grid">
            <div
              v-for="event in events"
              :key="event.name"
              class="event-card"
            >
              <h3>{{ event.name }}</h3>

              <p>{{ event.description }}</p>

              <p>📅 {{ event.date }}</p>
              <p>⏰ {{ event.startTime }} - {{ event.endTime }}</p>
              <p>📍 {{ event.location }}</p>
              <p>🏠 {{ event.address }}</p>
              <p>🌳 {{ event.trees }} trees</p>

              <p>
                <strong>{{ event.places }}</strong>
                places left
              </p>

              <p>
                Rating:
                <strong>{{ eventAverage(event.name) }}</strong>/5
              </p>
            </div>
          </div>
        </section>

        <section class="booking-section">
          <h2>Join an Activity</h2>

          <div v-if="hasBooking">
            <p><strong>Name:</strong> {{ registeredUser.name }}</p>
            <p><strong>Email:</strong> {{ registeredUser.email }}</p>
            <p><strong>Activity:</strong> {{ registeredUser.event }}</p>

            <button @click="cancelBooking">
              Cancel Registration
            </button>
          </div>

          <form v-else @submit.prevent="register">
            <input
              v-model.trim="name"
              type="text"
              placeholder="Full Name"
              maxlength="50"
              required
            >

            <input
              v-model.trim="email"
              type="email"
              placeholder="Email Address"
              maxlength="100"
              required
            >

            <select v-model="selectedEvent" required>
              <option value="">Choose an activity</option>

              <option
                v-for="event in events"
                :key="event.name"
                :value="event.name"
                :disabled="event.places === 0"
              >
                {{ event.name }} ({{ event.places }} places left)
              </option>
            </select>

            <button type="submit">
              Register Now
            </button>
          </form>
        </section>

        <section class="review-section">
          <h2>Reviews & Ratings</h2>

          <form @submit.prevent="submitReview">
            <select v-model="reviewEvent" required>
              <option value="">Choose an activity</option>

              <option
                v-for="event in events"
                :key="event.name"
                :value="event.name"
              >
                {{ event.name }}
              </option>
            </select>

            <select v-model.number="reviewRating" required>
              <option value="">Rating</option>
              <option
                v-for="rating in 5"
                :key="rating"
                :value="rating"
              >
                {{ rating }} / 5
              </option>
            </select>

            <textarea
              v-model.trim="reviewComment"
              placeholder="Write your review"
              maxlength="300"
              required
            ></textarea>

            <button type="submit">
              Submit Review
            </button>
          </form>

          <div
            v-for="review in reviews"
            :key="review.id"
            class="review"
          >
            <strong>{{ review.user }}</strong>

            <p>
              {{ review.rating }}/5
            </p>

            <p>{{ review.comment }}</p>
          </div>
        </section>

        <p v-if="message" :class="messageType">
          {{ message }}
        </p>
      </section>

      <section
        v-if="currentUser && currentUser.role === 'Admin'"
        class="admin-interface"
      >
        <h2>Admin Dashboard</h2>

        <div class="stats-bar">
          <div>
            <strong>{{ users.length }}</strong>
            <span>Users</span>
          </div>

          <div>
            <strong>{{ events.length }}</strong>
            <span>Activities</span>
          </div>

          <div>
            <strong>{{ reviews.length }}</strong>
            <span>Reviews</span>
          </div>
        </div>

        <h3>Registered Users</h3>

        <div
          v-for="user in users"
          :key="user.email"
          class="admin-item"
        >
          <p><strong>Name:</strong> {{ user.name }}</p>
          <p><strong>Email:</strong> {{ user.email }}</p>
          <p><strong>Role:</strong> {{ user.role }}</p>
        </div>

        <h3>Activities</h3>

        <div
          v-for="event in events"
          :key="event.name"
          class="admin-item"
        >
          <h4>{{ event.name }}</h4>
          <p>Places: {{ event.places }}</p>
          <p>Average Rating: {{ eventAverage(event.name) }}/5</p>
        </div>

        <h3>Reviews</h3>

        <div
          v-for="review in reviews"
          :key="review.id"
          class="admin-item"
        >
          <p><strong>User:</strong> {{ review.user }}</p>
          <p><strong>Activity:</strong> {{ review.event }}</p>
          <p><strong>Rating:</strong> {{ review.rating }}/5</p>
          <p>{{ review.comment }}</p>
        </div>
      </section>
    </main>

    <footer>
      <p>© 2026 GreenLink Melbourne</p>
    </footer>
  </div>
</template>

<script setup>
import { ref, computed, onMounted } from 'vue'

const events = ref([
  {
    name: 'Clayton Native Tree Planting',
    description: 'Help plant native canopy trees in the Clayton reserve area.',
    date: '1 September 2026',
    startTime: '9:00am',
    endTime: '12:00pm',
    location: 'Clayton',
    address: 'Clayton Local Park',
    trees: 100,
    places: 40
  },
  {
    name: 'Brunswick Native Garden Day',
    description: 'Help plant native shrubs in the local community garden.',
    date: '8 September 2026',
    startTime: '1:00pm',
    endTime: '4:00pm',
    location: 'Brunswick',
    address: 'Brunswick Community Hub',
    trees: 80,
    places: 30
  },
  {
    name: 'Australian Native Plants Workshop',
    description: 'Learn about biodiversity and plant habitat-friendly flora.',
    date: '15 September 2026',
    startTime: '8:00am',
    endTime: '11:00am',
    location: 'Sunshine',
    address: 'Sunshine Environment Centre',
    trees: 50,
    places: 20
  }
])

const users = ref([])
const reviews = ref([])
const currentUser = ref(null)

const authMode = ref('login')
const accountName = ref('')
const accountEmail = ref('')
const accountPassword = ref('')

const name = ref('')
const email = ref('')
const selectedEvent = ref('')
const hasBooking = ref(false)
const registeredUser = ref({
  name: '',
  email: '',
  event: ''
})

const reviewEvent = ref('')
const reviewRating = ref(0)
const reviewComment = ref('')

const message = ref('')
const messageType = ref('info')

const totalPlaces = computed(() =>
  events.value.reduce((total, event) => total + event.places, 0)
)

onMounted(() => {
  const savedUsers = localStorage.getItem('greenlinkUsers')
  const savedReviews = localStorage.getItem('greenlinkReviews')
  const savedUser = localStorage.getItem('greenlinkCurrentUser')
  const savedBooking = localStorage.getItem('greenlinkBooking')

  users.value = savedUsers ? JSON.parse(savedUsers) : []
  reviews.value = savedReviews ? JSON.parse(savedReviews) : []

  if (!users.value.some(user => user.role === 'Admin')) {
    users.value.push({
      name: 'GreenLink Administrator',
      email: 'admin@greenlink.com',
      password: 'Admin123!',
      role: 'Admin'
    })

    saveUsers()
  }

  if (savedUser) {
    currentUser.value = JSON.parse(savedUser)
  }

  if (savedBooking) {
    registeredUser.value = JSON.parse(savedBooking)
    hasBooking.value = true
  }
})

function saveUsers() {
  localStorage.setItem(
    'greenlinkUsers',
    JSON.stringify(users.value)
  )
}

function saveReviews() {
  localStorage.setItem(
    'greenlinkReviews',
    JSON.stringify(reviews.value)
  )
}

function registerAccount() {
  message.value = ''

  const cleanName = accountName.value.trim()
  const cleanEmail = accountEmail.value.trim().toLowerCase()

  if (cleanName.length < 2) {
    showMessage('Name must contain at least 2 characters.', 'error')
    return
  }

  if (!/^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(cleanEmail)) {
    showMessage('Please enter a valid email address.', 'error')
    return
  }

  if (accountPassword.value.length < 6) {
    showMessage('Password must be at least 6 characters.', 'error')
    return
  }

  if (users.value.some(user => user.email === cleanEmail)) {
    showMessage('This email is already registered.', 'error')
    return
  }

  users.value.push({
    name: cleanName,
    email: cleanEmail,
    password: accountPassword.value,
    role: 'User'
  })

  saveUsers()

  accountName.value = ''
  accountEmail.value = ''
  accountPassword.value = ''

  showMessage('Registration successful. Please login.', 'success')
  authMode.value = 'login'
}

function login() {
  const cleanEmail = accountEmail.value.trim().toLowerCase()

  const user = users.value.find(
    account =>
      account.email === cleanEmail &&
      account.password === accountPassword.value
  )

  if (!user) {
    showMessage('Invalid email or password.', 'error')
    return
  }

  currentUser.value = {
    name: user.name,
    email: user.email,
    role: user.role
  }

  localStorage.setItem(
    'greenlinkCurrentUser',
    JSON.stringify(currentUser.value)
  )

  accountEmail.value = ''
  accountPassword.value = ''

  showMessage('Login successful.', 'success')
}

function logout() {
  currentUser.value = null
  localStorage.removeItem('greenlinkCurrentUser')
  hasBooking.value = false
}

function register() {
  const cleanName = name.value.trim()
  const cleanEmail = email.value.trim().toLowerCase()

  if (cleanName.length < 2) {
    showMessage('Please enter a valid name.', 'error')
    return
  }

  if (!/^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(cleanEmail)) {
    showMessage('Please enter a valid email.', 'error')
    return
  }

  if (!selectedEvent.value) {
    showMessage('Please select an activity.', 'error')
    return
  }

  const targetEvent = events.value.find(
    event => event.name === selectedEvent.value
  )

  if (!targetEvent || targetEvent.places <= 0) {
    showMessage('This activity is full.', 'error')
    return
  }

  targetEvent.places--

  registeredUser.value = {
    name: cleanName,
    email: cleanEmail,
    event: selectedEvent.value
  }

  localStorage.setItem(
    'greenlinkBooking',
    JSON.stringify(registeredUser.value)
  )

  hasBooking.value = true

  name.value = ''
  email.value = ''
  selectedEvent.value = ''

  showMessage('Registration successful.', 'success')
}

function cancelBooking() {
  const event = events.value.find(
    item => item.name === registeredUser.value.event
  )

  if (event) {
    event.places++
  }

  localStorage.removeItem('greenlinkBooking')

  hasBooking.value = false
  registeredUser.value = {
    name: '',
    email: '',
    event: ''
  }

  showMessage('Registration cancelled.', 'success')
}

function submitReview() {
  if (!reviewEvent.value) {
    showMessage('Please select an activity.', 'error')
    return
  }

  if (reviewRating.value < 1 || reviewRating.value > 5) {
    showMessage('Rating must be between 1 and 5.', 'error')
    return
  }

  if (reviewComment.value.length < 5) {
    showMessage('Review must contain at least 5 characters.', 'error')
    return
  }

  reviews.value.push({
    id: Date.now(),
    user: currentUser.value.name,
    event: reviewEvent.value,
    rating: reviewRating.value,
    comment: reviewComment.value
  })

  saveReviews()

  reviewEvent.value = ''
  reviewRating.value = 0
  reviewComment.value = ''

  showMessage('Review submitted successfully.', 'success')
}

function eventAverage(eventName) {
  const list = reviews.value.filter(
    review => review.event === eventName
  )

  if (list.length === 0) {
    return 'No ratings'
  }

  const total = list.reduce(
    (sum, review) => sum + review.rating,
    0
  )

  return (total / list.length).toFixed(1)
}

function showMessage(text, type) {
  message.value = text
  messageType.value = type
}
</script>
```
