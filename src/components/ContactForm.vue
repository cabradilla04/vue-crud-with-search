<template>
  <div class="text-center">
    <v-dialog
      v-model="visible"
      width="450">
      <v-card>
        <v-card-title class="headline teal lighten-1" color="white">
          Add New Contact
        </v-card-title>

        <v-card-text class="pa-5">
          <form @submit.prevent="submit">
            <v-row>
              <v-col
                class="pa-2"
                cols="12">
                <v-text-field
                  label="First Name"
                  v-model="contact.firstname"
                  outlined
                  dense
                  hide-details></v-text-field>
              </v-col>
              <v-col
                class="pa-2"
                cols="12">
                <v-text-field
                  label="Last Name"
                  v-model="contact.lastname"
                  outlined
                  dense
                  hide-details></v-text-field>
              </v-col>
              <v-col
                class="pa-2"
                cols="12">
                <v-row>
                  <v-col
                    class="pa-2"
                    cols="6">
                    <h4 class="pa-2">Email Address</h4>
                  </v-col>
                  <v-col
                    class="pa-2 text-right"
                    cols="6">
                    <v-btn
                      icon
                      color="teal lighten-1"
                      @click.prevent="addEmail">
                      <v-icon>mdi-plus</v-icon>
                    </v-btn>
                  </v-col>
                  <v-col
                    class="pt-2"
                    cols="12"
                    v-for="(addr, index) in contact.email"
                    :key="index">
                    <v-text-field
                      v-model="contact.email[index]"
                      outlined
                      dense
                      hide-details></v-text-field>
                  </v-col>
                </v-row>
              </v-col>
              <v-col
                class="pa-2"
                cols="12">
                <v-row>
                  <v-col
                    class="pa-2"
                    cols="6">
                    <h4 class="pa-2">Contact Number</h4>
                  </v-col>
                  <v-col
                    class="pa-2 text-right"
                    cols="6">
                    <v-btn
                      icon
                      color="teal lighten-1"
                      @click.prevent="addPhone">
                      <v-icon>mdi-plus</v-icon>
                    </v-btn>
                  </v-col>
                  <v-col
                    class="pt-2"
                    cols="12"
                    v-for="(number, index) in contact.phoneNumber"
                    :key="index">
                    <v-text-field
                      v-model="contact.phoneNumber[index]"
                      outlined
                      dense
                      hide-details></v-text-field>
                  </v-col>
                </v-row>
              </v-col>
            </v-row>
          </form>
        </v-card-text>

        <v-divider></v-divider>

        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn
            color="error"
            dark
            @click.prevent="cancel">
            cancel
          </v-btn>
          <v-btn
            color="teal lighten-1"
            dark
            @click.prevent="submit">
            Submit
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </div>
</template>

<script>
/**
 * Event bus for contact
 */
import { ContactBus } from '../bus/ContactBus'

export default {
  name: 'ContactForm',
  props: {
    contact: {
      type: Object,
      required: true
    },
    visible: {
      type: Boolean
    },
    newCon: {
      type: Boolean
    },
    index: {
      type: [Number, String]
    }
  },
  computed: {
    validatedEmail: function () {
      var format = /^[a-zA-Z0-9.!#$%&'*+/=?^_`{|}~-]+@[a-zA-Z0-9-]+(?:\.[a-zA-Z0-9-]+)*$/
      for (var i = 0; i < this.contact.email.length; i++) {
        if (this.contact.email[i] !== '' && !this.contact.email[i].match(format)) {
          return false
        }
      }
      return true
    },
    validatedPhone: function () {
      for (var i = 0; i < this.contact.phoneNumber.length; i++) {
        if (this.contact.phoneNumber[i] !== '' && this.contact.phoneNumber[i].length <= 11) {
          return false
        }
      }
      return true
    }
  },
  methods: {
    submit: function () {
      if (this.contact.firstname && this.contact.lastname) {
        if (this.validatedEmail || this.validatedPhone) {
          ContactBus.$emit('addCon', this.contact)
        } else {
          ContactBus.$emit('noAddCon', [])
        }
      } else {
        ContactBus.$emit('noAddCon', [])
      }
    },
    purgeNumber: function (index) {
      this.contact.phoneNumber[index] = this.contact.phoneNumber[index].replace(/\D/g, '')
    },
    cancel: function () {
      ContactBus.$emit('close', true)
    },
    removeContact: function () {
      ContactBus.$emit('delCon', this.index)
    },
    addEmail: function () {
      this.contact.email.push('')
    },
    addPhone: function () {
      this.contact.phoneNumber.push('')
    }
  }
}
</script>

<style scoped>
.v-text-field{
  width: 100% !important;
}

.v-card__title {
  color: white !important;
}
</style>
