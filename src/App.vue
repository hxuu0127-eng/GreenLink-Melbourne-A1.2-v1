<script setup>

</script>

<template>
  <header>
    <h1>GreenLink Melbourne</h1>
    <p>Urban greening, tree planting and biodiversity</p>
  </header>

  <main>
    <section>
      <h2>Upcoming Activities</h2>

      <div class="events">
        <div
          v-for="event in events"
          :key="event.name"
          class="event-card"
        >
          <h3>{{ event.name }}</h3>
          <p><strong>Date:</strong> {{ event.date }}</p>
          <p><strong>Location:</strong> {{ event.location }}</p>
          <p><strong>Available places:</strong> {{ event.places }}</p>
        </div>
      </div>
    </section>

    <section class="booking">
      <h2>Book an Activity</h2>

      <input
        v-model="name"
        type="text"
        placeholder="Your name"
      />

      <input
        v-model="email"
        type="email"
        placeholder="Your email"
      />

      <select v-model="selectedEvent">
        <option value="">Choose an activity</option>

        <option
          v-for="event in events"
          :key="event.name"
          :value="event.name"
        >
          {{ event.name }}
        </option>
      </select>

      <button @click="register">
        Register
      </button>

      <p class="message">{{ message }}</p>
    </section>
  </main>
</template>