<template>
  <div class="home-view">
    <h1>Contact Book</h1>
    <input v-model="filter" @input="filterContacts" placeholder="Search contacts..." />
    <ul>
      <li v-for="contact in sortedFilteredContacts" :key="contact.id">
        {{ contact.firstName }} {{ contact.lastName }}
        <button @click="showContact(contact)" aria-label="View contact details">View</button>
        <button @click="deleteContact(contact)" aria-label="Delete contact">Delete</button>
      </li>
    </ul>
    <p v-if="sortedFilteredContacts.length === 0">No contacts found.</p>
    <form @submit.prevent="addContact">
      <input v-model="newContact.firstName" placeholder="First Name" />
      <input v-model="newContact.lastName" placeholder="Last Name" />
      <input v-model="newContact.email" placeholder="Email" />
      <input v-model="newContact.phone" placeholder="Phone Number" />
      <button type="submit">Add Contact</button>
    </form>
  </div>
  <router-view />
</template>

<script setup lang="ts">
import { ref, computed, watch, onMounted } from 'vue'
import { useRouter } from 'vue-router'

const router = useRouter()

const filter = ref('')
const newContact = ref({ firstName: '', lastName: '', email: '', phone: '' })

const contacts = ref([
  { id: 1, firstName: 'Judith', lastName: 'Robert', email: 'judyr@roberts.com', phone: '1234567890' },
  { id: 2, firstName: 'Gasa', lastName: 'Robert', email: 'gasa@roberts.com', phone: '0987654321' },
  { id: 3, firstName: 'Alice', lastName: 'Robert', email: 'alice@roberts.com', phone: '1112223333' },
  { id: 4, firstName: 'Sandra', lastName: 'Robert', email: 'sandra@roberts.com', phone: '4445556666' },
  { id: 5, firstName: 'Ishimwe', lastName: 'Robert', email: 'ishimwe@roberts.com', phone: '7778889999' },
  { id: 6, firstName: 'Jay', lastName: 'Robert', email: 'jay@roberts.com', phone: '1011121314' }
])

const filteredContacts = computed(() => {
  return contacts.value.filter(contact =>
    contact.firstName.toLowerCase().includes(filter.value.toLowerCase()) ||
    contact.lastName.toLowerCase().includes(filter.value.toLowerCase()) ||
    contact.email.toLowerCase().includes(filter.value.toLowerCase()) ||
    contact.phone.toString().toLowerCase().includes(filter.value.toLowerCase())
  )
})

const sortedFilteredContacts = computed(() => {
  return [...filteredContacts.value].sort((a, b) => 
    a.lastName.localeCompare(b.lastName)
  )
})

function showContact(contact) {
  router.push(`/contact/${contact.id}`) // Fixed route string
}

function deleteContact(contact) {
  contacts.value = contacts.value.filter(c => c.id !== contact.id)
  saveContactsToLocalStorage()
}

function addContact() {
  if (!newContact.value.firstName || !newContact.value.lastName || !newContact.value.email) {
    alert('Please fill in all required fields.')
    return
  }
  const newId = Date.now()
  contacts.value.push({ id: newId, ...newContact.value })
  newContact.value = { firstName: '', lastName: '', email: '', phone: '' }
  saveContactsToLocalStorage()
}

function editContact(id) {
  const contactIndex = contacts.value.findIndex(c => c.id === id)
  if (contactIndex > -1) {
    contacts.value[contactIndex] = { ...contacts.value[contactIndex], ...newContact.value }
    newContact.value = { firstName: '', lastName: '', email: '', phone: '' }
    saveContactsToLocalStorage()
  }
}

function saveContactsToLocalStorage() {
  localStorage.setItem('contacts', JSON.stringify(contacts.value))
}

function loadContactsFromLocalStorage() {
  const storedContacts = localStorage.getItem('contacts')
  if (storedContacts) {
    contacts.value = JSON.parse(storedContacts)
  }
}

onMounted(() => {
  loadContactsFromLocalStorage()
})

watch(contacts, () => {
  saveContactsToLocalStorage()
}, { deep: true })

function editContactForm(id) {
  const contactIndex = contacts.value.findIndex(c => c.id === id)
  if (contactIndex > -1) {
    newContact.value = { ...contacts.value[contactIndex] }
  }
}
</script>

