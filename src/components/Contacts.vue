<template>
  <div id="contact-list">
    <v-container>
      <v-card elevation="2">
        <v-card-title>
        </v-card-title>
        <v-data-table
          :headers="headers"
          :items="contacts"
          :search="search">
          <template v-slot:item.actions="{ item, index }">
            <v-icon
              small
              class="mr-2"
              @click="edit(index)"
            >
              mdi-pencil
            </v-icon>
            <v-icon
              small
              @click="deleteCon(item)"
            >
              mdi-delete
            </v-icon>
          </template>
          <template v-slot:no-data>
            <h4>No Results Found</h4>
          </template>
        </v-data-table>
      </v-card>
    </v-container>
  </div>
</template>
<script>
import { ContactBus } from '../bus/ContactBus'

export default {
  name: 'Contact',
  data () {
    return {
      search: '',
      headers: [
        {
          text: 'Last Name',
          sortable: false,
          value: 'lastname'
        },
        {
          text: 'First Name',
          sortable: false,
          value: 'firstname'
        },
        {
          text: 'Emails',
          sortable: false,
          value: 'email'
        },
        {
          text: 'Contact Numbers',
          sortable: false,
          value: 'phoneNumber'
        },
        {
          text: 'Actions',
          value: 'actions'
        }
      ]
    }
  },
  methods: {
    edit: function (index) {
      ContactBus.$emit('editCon', index)
    },
    deleteCon: function (item) {
      // console.log(item)
      ContactBus.$emit('delCon', item)
    }
  },
  props: {
    contacts: {
      type: Array
    }
  }
}
</script>
<style scoped>
ul {
  list-style-type:none;
}
ul li {
  display: inline-block;
  min-width: 200px;
  max-width: 300px;
}
</style>
