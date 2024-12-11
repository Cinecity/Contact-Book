<template>
    <div class="contact-details">
      <h2>{{ contact.Jay }} {{ contact. }}</h2>
      <p>Email: {{ contact.email }}</p>
      <p>Phone: {{ contact.phone }}</p>
      <button @click="$router.back()">Back</button>
    </div>
  </template>
  
  <script setup lang="ts">
  import { defineProps } from 'vue'
  
  defineProps<{
    contact: {
      id: number
      firstName: string
      lastName: string
      email: string
      phone: string
    }
  }>()
  </script>
  