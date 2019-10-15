<template>
  <div class="home">
    <h1>{{ message }}</h1>
    <h2>New Contact</h2>
    <div>
      First Name: <input type="text" v-model="newFirstName"><br>
      Last Name: <input type="text" v-model="newLastName"><br>
      Email: <input type="text" v-model="newEmail"><br>
      Phone Number: <input type="text" v-model="newPhoneNumber"><br>
    </div>
    <button v-on:click="createContact()">Create Contact</button>

    <h2>All Contacts</h2>
    {{ currentContact }}

    <div v-for="contact in contacts">
      <h2>Full Name: {{ contact.full_name }}</h2>
      <div>
        <button v-on:click="showContact(contact)">More Info</button>
      </div>
      <div v-if="currentContact === contact">
        <p>First Name: {{ contact.first_name }}</p>
        <p>Last Name: {{ contact.last_name }}</p>
        <p>Email: {{ contact.email }}</p>
        <p>Phone Number: {{ contact.phone_number }}</p><br>
        <h3>Edit Contact</h3>
        <div>
          First Name: <input type="text" v-model="contact.first_name"><br>
          Last Name: <input type="text" v-model="contact.last_name"><br>
          Email: <input type="text" v-model="contact.email"><br>
          Phone Number: <input type="text" v-model="contact.phone_number"><br>
          <button v-on:click="updateContact(contact)">Update</button><br>
          <button v-on:click="destroyRecipe(recipe)">Destroy</button><br>
          {{contact}}
        </div>
      </div>
    </div>
  </div>
</template>

<style>
</style>

<script>
import axios from "axios";
export default {
  data: function() {
    return {
      message: "CONTACTS",
      contacts: [],
      newFirstName: "",
      newLastName: "",
      newEmail: "",
      newPhoneNumber: "",
      currentContact: {}
    };
  },
  created: function() {
    axios.get("/api/contacts").then(response => {
      this.contacts = response.data;
      console.log(this.contacts);
    });
  },
  methods: {
    createContact: function() {
      var params = {
        first_name: this.newFirstName,
        last_name: this.newLastName,
        email: this.newEmail,
        phone_number: this.newPhoneNumber
      };
      axios
        .post("/api/contacts", params)
        .then(response => {
          console.log("Success", response.data);
          this.contacts.push(response.data);
          this.newFirstName = "";
          this.newLastName = "";
          this.newEmail = "";
          this.newPhoneNumber = "";
        })
        .catch(error => {
          console.log(error.response.data.errors);
        });
    },
    showContact: function(contact) {
      if (this.currentContact === contact) {
        this.currentContact = {};
      } else {
        this.currentContact = contact;
      }
    },
    updateContact: function(contact) {
      var params = {
        first_name: contact.first_name,
        last_name: contact.last_name,
        email: contact.email,
        phone_number: contact.phone_number
      };
      axios
        .patch("/api/contacts/" + contact.id, params)
        .then(response => {
          console.log("Success", response.data);
        })
        .catch(error => {
          console.log(error.response.data.errors);
        });
    },
    destroyContact: function(contact) {
      axios.delete("/api/contacts/" + contact.id)
        .then(response => {
          console.log("Success", response.data);
          var index = this.contacts.indexOf(contact);
          this.contacts.splice(index, 1);
        });
    }
  }
};
</script>


<!-- Define the HTML view in the <template>
Make a button to show/hide additional information
Make a <div> with a v-if surrounding the additional information
Define the JavaScript data in the <script>
Make an empty object to keep track of the current item
Define a JavaScript show method in the methods object
Toggle the current item to be the item that was clicked or an empty object

 -->