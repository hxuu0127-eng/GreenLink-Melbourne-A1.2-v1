<template>
  <div class="app-container">
    <!-- Header Section -->
    <header class="main-header">
      <div class="header-content">
        <h1>GreenLink Melbourne</h1>
        <p class="subtitle">Topic: Urban greening, tree planting and biodiversity</p>
      </div>
    </header>

    <!-- Main Content -->
    <main class="main-content">
      <!-- Introduction Section -->
      <section class="intro-section">
        <h2>Welcome to GreenLink Melbourne</h2>
        <p>Join us in shaping a greener future! We are dedicated to urban greening, tree planting, and enhancing biodiversity across Melbourne's suburbs. Explore our upcoming volunteer activities below and register to make a difference.</p>
        <div class="stats-bar">
          <div class="stat-item"><strong>300+</strong> Trees Planted</div>
          <div class="stat-item"><strong>3</strong> Suburbs Active</div>
          <div class="stat-item"><strong>90+</strong> Open Spots</div>
        </div>
      </section>

      <!-- Activities Grid Section -->
      <section class="events-section">
        <div class="section-title-wrap">
          <h2>Upcoming Activities</h2>
          <span class="badge">Volunteer Opportunities</span>
        </div>

        <div class="events-grid">
          <div 
            v-for="event in events" 
            :key="event.name" 
            class="event-card"
            :class="{ 'sold-out': event.places === 0 }"
          >
            <div class="card-header">
              <h3>{{ event.name }}</h3>
              <span class="status-tag" :class="event.places > 0 ? 'available' : 'full'">
                {{ event.places > 0 ? 'Open' : 'Full' }}
              </span>
            </div>
            
            <div class="card-body">
              <p class="desc">{{ event.description }}</p>
              <div class="meta-info">
                <p><strong>📅 Date:</strong> {{ event.date }}</p>
                <p><strong>⏰ Time:</strong> {{ event.startTime }} - {{ event.endTime }}</p>
                <p><strong>📍 Region:</strong> {{ event.location }}</p>
                <p><strong>🏠 Address:</strong> {{ event.Address }}</p>
                <p><strong>🌳 Target Trees:</strong> {{ event.trees }}</p>
              </div>
            </div>

            <div class="card-footer">
              <span class="places-left">🔥 Only <strong>{{ event.places }}</strong> places left</span>
            </div>
          </div>
        </div>
      </section>

      <!-- Booking Form Section -->
      <section class="booking-section">
        <h2>Please choose an Activity to join</h2>
        <p class="form-intro">Fill out the form below to secure your spot. Confirmation details will be sent via email.</p>

        <!-- Displayed when user already has a registration -->
        <div v-if="hasBooking" class="current-booking-box">
          <h3>Your Current Registration</h3>
          <p><strong>Name:</strong> {{ registeredUser.name }}</p>
          <p><strong>Email:</strong> {{ registeredUser.email }}</p>
          <p><strong>Activity:</strong> {{ registeredUser.event }}</p>
          <button @click="cancelBooking" class="btn-cancel">Cancel Registration</button>
        </div>

        <!-- Registration Form -->
        <form v-else @submit.prevent="register" class="booking-form">
          <div class="form-group">
            <label for="userName">Full Name</label>
            <input 
              id="userName"
              v-model="name" 
              type="text" 
              placeholder="Enter your full name"
              required
            >
          </div>

          <div class="form-group">
            <label for="userEmail">Email Address</label>
            <input 
              id="userEmail"
              v-model="email" 
              type="email" 
              placeholder="example@domain.com"
              required
            >
          </div>

          <div class="form-group password-group">
            <label for="userPassword">Password</label>
            <div class="password-input-wrapper">
              <input 
                id="userPassword"
                v-model="password" 
                :type="showPassword ? 'text' : 'password'" 
                placeholder="At least 6 characters"
                required
              >
              <button 
                type="button" 
                class="btn-toggle-password" 
                @click="showPassword = !showPassword"
              >
                {{ showPassword ? '👁️' : '🙈' }}
              </button>
            </div>
          </div>

          <div class="form-group">
            <label for="eventSelect">Select Activity</label>
            <select id="eventSelect" v-model="selectedEvent" required>
              <option value="">Choose an activity...</option>
              <option 
                v-for="event in events" 
                :key="event.name" 
                :value="event.name"
                :disabled="event.places === 0"
              >
                {{ event.name }} ({{ event.places }} places left)
              </option>
            </select>
          </div>

          <button type="submit" class="btn-submit" :disabled="isSubmitting">
            {{ isSubmitting ? 'Processing...' : 'Register Now' }}
          </button>
        </form>

        <!-- Dynamic Message Banner -->
        <transition name="fade">
          <p v-if="message" class="message-banner" :class="messageType">
            {{ message }}
          </p>
        </transition>
      </section>

      <!-- Impact History Section -->
      <section class="history-section">
        <h2>Our Impact Gallery</h2>
        <p>Take a look at our past greening achievements across Melbourne communities.</p>
        <div class="gallery-placeholder">
          <div class="gallery-item">🌱 Royal Park Re-vegetation (Completed)</div>
          <div class="gallery-item">🌿 St Kilda Coastal Planting (Completed)</div>
          <div class="gallery-item">🌲 Richmond Community Nursery (Completed)</div>
        </div>
      </section>
    </main>

    <!-- Footer Section -->
    <footer class="main-footer">
      <p>&copy; 2026 GreenLink Melbourne. All rights reserved.</p>
      <p>Building a sustainable and biodiverse urban ecosystem together.</p>
    </footer>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'

const events = ref([
  {
    name: 'Hang Xu',
    description: 'Help plant some native canopy trees in the Clayton reserve area.',
    date: '1 September 2026',
    startTime: '9:00am',
    endTime: '12:00pm',
    location: 'Clayton',
    trees: 100,
    Address: 'Clayton Local Park (Exact spot via email)',
    places: 40
  },
  {
    name: 'Brunswick Native Garden Day',
    description: 'Help plant native shrubs and bushes in the local community garden.',
    date: '8 September 2026',
    startTime: '1:00pm',
    endTime: '4:00pm',
    location: 'Brunswick',
    trees: 80,
    Address: 'Brunswick Community Hub',
    places: 30
  },
  {
    name: 'Australian Native Plants Workshop',
    description: 'Learn from biodiversity experts and plant habitat-friendly flora.',
    date: '15 September 2026',
    startTime: '8:00am',
    endTime: '11:00am',
    location: 'Sunshine',
    trees: 50,
    Address: 'Sunshine Environment Centre',
    places: 20
  }
])

const name = ref('')
const email = ref('')
const password = ref('')
const showPassword = ref(false)
const selectedEvent = ref('')
const message = ref('')
const messageType = ref('info') 
const isSubmitting = ref(false)

const hasBooking = ref(false)
const registeredUser = ref({ name: '', email: '', event: '' })

onMounted(() => {
  const savedBooking = localStorage.getItem('greenlinkBooking')
  if (savedBooking) {
    try {
      registeredUser.value = JSON.parse(savedBooking)
      hasBooking.value = true
    } catch (e) {
      localStorage.removeItem('greenlinkBooking')
    }
  }
})

function register() {
  message.value = ''
  isSubmitting.value = true

  if (name.value.trim() === '') {
    message.value = 'The name cannot be empty or just spaces.'
    messageType.value = 'error'
    isSubmitting.value = false
    return
  }

  const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/
  if (!emailRegex.test(email.value)) {
    message.value = 'Please enter a valid email address (e.g., name@example.com).'
    messageType.value = 'error'
    isSubmitting.value = false
    return
  }

  if (password.value.length < 6) {
    message.value = 'Password must be at least 6 characters long.'
    messageType.value = 'error'
    isSubmitting.value = false
    return
  }

  if (selectedEvent.value === '') {
    message.value = 'Please choose a valid greening activity.'
    messageType.value = 'error'
    isSubmitting.value = false
    return
  }

  const targetEvent = events.value.find(e => e.name === selectedEvent.value)
  if (!targetEvent) {
    message.value = 'Selected activity does not exist.'
    messageType.value = 'error'
    isSubmitting.value = false
    return
  }

  if (targetEvent.places <= 0) {
    message.value = 'Sorry, this activity is already full.'
    messageType.value = 'error'
    isSubmitting.value = false
    return
  }

  targetEvent.places -= 1
  
  const bookingData = {
    name: name.value,
    email: email.value,
    event: selectedEvent.value
  }

  localStorage.setItem('greenlinkBooking', JSON.stringify(bookingData))
  registeredUser.value = bookingData
  hasBooking.value = true

  message.value = 'Registration successful! Confirmation has been stored.'
  messageType.value = 'success'
  isSubmitting.value = false

  name.value = ''
  email.value = ''
  password.value = ''
}

function cancelBooking() {
  const targetEvent = events.value.find(e => e.name === registeredUser.value.event)
  if (targetEvent) {
    targetEvent.places += 1
  }

  localStorage.removeItem('greenlinkBooking')
  hasBooking.value = false
}
</script>

<style scoped>
.app-container {
  max-width: 1200px;
  margin: 0 auto;
  padding: 15px;
}

.stats-bar {
  display: flex;
  flex-direction: column;
  gap: 15px;
  margin-top: 20px;
}

.events-grid, .gallery-placeholder {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(280px, 1fr));
  gap: 20px;
  margin-top: 20px;
}

@media (min-width: 768px) {
  .stats-bar {
    flex-direction: row;
    justify-content: space-around;
  }
  
  .booking-form {
    display: grid;
    grid-template-columns: 1fr 1fr;
    gap: 20px;
  }
  
  .form-group.password-group,
  .btn-submit {
    grid-column: span 2; 
  }
}

input, select, .password-input-wrapper {
  width: 100%;
  box-sizing: border-box;
}
</style>
