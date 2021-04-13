<template>
  <div id="app">
    <v-app>
      <v-app-bar app color="teal lighten-1" dark>
        <v-icon
          class="pa-2"
          large
          color="white">
          mdi-account-box
        </v-icon>
        <v-toolbar-title>Contact Management</v-toolbar-title>
      </v-app-bar>
      <v-main>
        <v-container fluid>
          <div class="pa-5">
            <v-row class="justify-center">
              <v-col></v-col>
              <v-col>
                <v-text-field
                  id="search"
                  dense
                  outlined
                  v-model="query"
                  label="Search"
                  color="teal lighten-1"
                  append-icon="mdi-magnify"
                  hide-details="auto"></v-text-field>
              </v-col>
              <v-col></v-col>
            </v-row>
          </div>
          <app-contactform v-bind="formSettings" v-bind:contact="contact"></app-contactform>
          <app-contacts v-bind:contacts="filteredContacts"></app-contacts>
          <v-fab-transition>
            <v-btn
              @click.prevent="openNew"
              color="teal lighten-1"
              dark
              absolute
              bottom
              right
              fab>
              <v-icon>mdi-plus</v-icon>
            </v-btn>
          </v-fab-transition>
        </v-container>
      </v-main>
    </v-app>
  </div>
</template>

<script>
import Contacts from './components/Contacts'
import ContactForm from './components/ContactForm'
import { ContactBus } from './bus/ContactBus'

export default {
  name: 'App',
  components: {
    'app-contacts': Contacts,
    'app-contactform': ContactForm
  },
  data () {
    return {
      contacts: '',
      contact: {
        firstname: '',
        lastname: '',
        email: [''],
        phoneNumber: ['']
      },
      formSettings: {
        visible: false,
        newCon: false,
        index: ''
      },
      query: ''
    }
  },
  computed: {
    filteredContacts () {
      return this.contacts.filter((contact) => {
        return this.query.toLowerCase().split(' ').every(key => contact.lastname.toLowerCase().includes(key) || contact.firstname.toLowerCase().includes(key))
      })
    }
  },
  methods: {
    sortContacts: function () {
      this.contacts.sort((a, b) => {
        return ((a.lastname.toLowerCase() === b.lastname.toLowerCase()) ? 0 : ((a.lastname.toLowerCase() > b.lastname.toLowerCase()) ? 1 : -1))
      })
    },
    openNew: function () {
      this.formSettings.visible = true
      this.formSettings.newCon = true
      this.contact = {
        id: '',
        firstname: '',
        lastname: '',
        email: [''],
        phoneNumber: ['']
      }
    },
    close: function (data) {
      this.formSettings.visible = !data
    }
  },
  created: function () {
    const mockContacts = require('./assets/contacts.json')
    const clone = JSON.parse(JSON.stringify(mockContacts))
    this.contacts = clone
    this.sortContacts()
    ContactBus.$on('addCon', (data) => {
      if (this.formSettings.newCon) {
        data.id = this.contacts.length + 1
        this.contacts.push(data)
        console.log(this.contacts)
        this.formSettings.visible = false
        this.formSettings.newCon = false
        this.formSettings.index = ''
        this.sortContacts()
      } else {
        this.formSettings.visible = false
        this.formSettings.newCon = false
        this.formSettings.index = ''
        this.sortContacts()
      }
    })

    ContactBus.$on('noAddCon', (data) => {
      this.nullContact = data
    })

    ContactBus.$on('close', (data) => {
      this.close(data)
    })

    ContactBus.$on('delCon', (data) => {
      const index = this.contacts.findIndex(function (item, i) {
        return item.id === data.id
      })
      this.contacts.splice(index, 1)
      console.log(index)
      this.formSettings.newCon = false
      this.formSettings.visible = false
      this.formSettings.index = ''
    })

    ContactBus.$on('editCon', (contactIndex) => {
      this.formSettings.newCon = false
      this.formSettings.visible = true
      this.formSettings.index = contactIndex
      this.contact.firstname = this.contacts[contactIndex].firstname
      this.contact.lastname = this.contacts[contactIndex].lastname
      this.contact.email = this.contacts[contactIndex].email
      this.contact.phoneNumber = this.contacts[contactIndex].phoneNumber
    })
  }
}

</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
  background-color: #E0F2F1;
}

.container {
  max-width: 80% !important;
}

.v-btn--fab.v-size--default.v-btn--absolute.v-btn--bottom {
  bottom: 16px !important;
}

.v-text-field{
  width: 400px !important;
}
</style>
