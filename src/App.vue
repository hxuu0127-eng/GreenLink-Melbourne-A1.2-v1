<template>
  <header>
    <h1>GreenLink Melbourne</h1>
    <p>Topic: Urban greening, tree planting and biodiversity</p>
  </header>

  <main>
    <section>
      <h2>Hi everyone, welcome to join Urban greening, tree planting and biodiversity</h2>
      <h2>Upcoming Activities</h2>

      <div class="events">
        <div v-for="event in events" :key="event.name" class="event-card">
          <h3>{{ event.name }}</h3>
          <p><strong>Description:</strong> {{ event.description }}</p>
          <p><strong>Date:</strong> {{ event.date }}</p>
          <p><strong>Start Time:</strong> {{ event.startTime }}</p>
          <p><strong>End Time:</strong> {{ event.endTime }}</p>
          <p><strong>Location:</strong> {{ event.location }}</p>
          <p><strong>Trees:</strong> {{ event.trees }}</p>
          <p><strong>Address:</strong> {{ event.Address }}</p>
          <p><strong>Available places:</strong> {{ event.places }}</p>
        </div>
      </div>
    </section>

    <section class="booking">
      <h2>Please choose an Activity to join</h2>

      <input v-model="name" type="text" placeholder="Your name">

      <input v-model="email" type="email" placeholder="Your email">

      <select v-model="selectedEvent">
        <option value="">Choose an activity</option>

        <option v-for="event in events" :key="event.name" :value="event.name">
          {{ event.name }}
        </option>
      </select>

      <button @click="register">Register</button>

      <p class="message">{{message}}</p>
    </section>
  </main>
</template>

<script setup>
import { ref } from 'vue'

const events = ref([
  {
    name: 'Clayton Tree Planting',
    description: 'Help plant some trees in Clayton.',
    date: '1 September 2026',
    startTime: '9:00am',
    endTime: '12:00am',
    location: 'Clayton',
    trees: 100,
    Address: 'Please wait for notification',
    places: 40
  },
  {
    name: 'Brunswick Native Garden Day',
    description: 'Help plant native trees in the local community area.',
    date: '8 September 2026',
    startTime: '1:00pm',
    endTime: '4:00pm',
    location: 'Sunbury',
    trees: 80,
    Address: 'Please wait for notification',
    places: 30
  },
  {
    name: 'Australian Native Plants Workshop',
    description: 'Help plant native trees during the workshop.',
    date: '15 September 2026',
    startTime: '8:00am',
    endTime: '11:00am',
    location: 'Sunshine',
    trees: 50,
    Address: 'Please wait for notification',
    places: 20
  }
])

const name = ref('')
const email = ref('')
const selectedEvent = ref('')
const message = ref('')

function register() {
  if (name.value.trim() === '') {
    message.value = 'The name can not be space.'
    return
  }

  if (!email.value.includes('@')) {
    message.value = 'Please enter a valid email, it must have "@".'
    return
  }

  if (selectedEvent.value === '') {
    message.value = 'Please choose an event.'
    return
  }

  const booking = {
    name: name.value,
    email: email.value,
    event: selectedEvent.value
  }

  localStorage.setItem('greenlinkBooking', JSON.stringify(booking))
  message.value = 'Booking submitted successfully!'
}
</script>

