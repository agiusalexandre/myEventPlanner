<template>
  <v-app>
    <v-navigation-drawer permanent app color="primary">
      <template v-slot:prepend>
        <v-list-item>          
            <v-list-item-title class="my-2 text-h5">My Event Planner</v-list-item-title>            
            <v-list-item-subtitle class="my-2">Find the best campus events!</v-list-item-subtitle>
        </v-list-item>
      </template>      
      <v-list v-for="item in menuItems" :key="item.menuText" @click="callMethod(item.methodName)">
        <v-list-item class="ma-1" link>
          <template v-slot:prepend>            
              <v-icon>{{ item.iconName }}</v-icon>            
          </template>
          <v-list-item-title>{{ item.menuText }}</v-list-item-title>
        </v-list-item>
      </v-list>
    </v-navigation-drawer>
    <v-app-bar app color="primary">
      <v-toolbar-title> {{  currentEventsView }}</v-toolbar-title>
      <v-spacer></v-spacer>
      <v-btn v-if="isAdmin" color="warning">Admin
        <v-dialog v-model="adminDialog" activator="parent" width="auto">
            <v-card>
            <v-card-text>
                Warning: This command will delete ALL the data in the database, and then add some mock (fake) data.
            </v-card-text>
            <v-card-actions>
                <v-btn color="warning" @click="adminTools">Proceed</v-btn>
                <v-btn color="primary" @click="adminDialog = false">Cancel</v-btn>
            </v-card-actions>
            </v-card>
        </v-dialog>
      </v-btn> 
      <v-btn color="secondary" @click="signOut">Sign Out</v-btn>
    </v-app-bar>
    <v-main>
      <v-container>
        <!-- This is where events will appear -->
      </v-container>
    </v-main>
  </v-app>
</template>

<script setup>
import { Auth } from 'aws-amplify'
import { deleteAllRecords, addMockRecords } from "../mock.js"

const menuItems = [
  {
    menuText: "All Upcoming Events",
    iconName: "mdi-calendar-arrow-right",
    methodName: "showAllUpcomingEvents",
  },
  {
    menuText: "All Past Events",
    iconName: "mdi-calendar-arrow-left",
    methodName: "showAllPastEvents",
  },
  {
    menuText: "My Tickets",
    iconName: "mdi-ticket",
    methodName: "showMyTickets",
  },
  {
    menuText: "My Planned Events",
    iconName: "mdi-calendar-edit",
    methodName: "showMyPlannedEvents",
  }
]
</script>

<script>
export default {
  data() {
    return {
      currentEventsView: "All Upcoming Events",
      isAdmin: false,
      adminDialog: false,   
    }
  },
  async mounted() {
    try {
      const userInfo = await Auth.currentAuthenticatedUser()
      const groups = userInfo.signInUserSession.accessToken.payload["cognito:groups"]
      if(groups && groups.includes('admins')) {
        this.isAdmin = true
      }
    } catch(err) {
      console.log("Error", err)   
    }
  },
  methods: {
    callMethod(methodName) {
      this[methodName]()
    },
    showAllUpcomingEvents() {
      this.currentEventsView = "All Upcoming Events"
      // Add your code here in later steps
    },
    showAllPastEvents() {
      this.currentEventsView = "All Past Events"
      // Add your code here in later steps
    },
    showMyTickets() {
      this.currentEventsView = "My Tickets"
      // Add your code here in later steps
    },
    showMyPlannedEvents() {
      this.currentEventsView = "My Planned Events"
      // Add your code here in later steps
    },
    adminTools() {
      this.currentEventsView = "Admin Tools";
      deleteAllRecords() 
      addMockRecords()
      this.adminDialog = false
    },
    async signOut() {
      await Auth.signOut()
      this.$router.push("/signin")
    }
  }
}
</script>
