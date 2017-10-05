<template>
  <section class="section">
    <div class="container">
      <h1 class="title">Google Calendar Play</h1>
      <button class="button" v-if="!signedIn" @click="handleAuthClick">Authorize</button>
      <button class="button" v-if="signedIn" @click="handleSignoutClick">SignOut</button>
      <ul>
        <li v-for="event in upcomingEvents">{{ event.summary }}</li>
      </ul>
      
    </div>
    
  </section>
</template>

<script>
export default {

  name: 'app',

  methods: {
    handleClientLoad() {
      gapi.load('client:auth2', this.initClient)
    },

    initClient() {
      gapi.client.init({
        discoveryDocs: ["https://www.googleapis.com/discovery/v1/apis/calendar/v3/rest"],
        clientId: '584697159634-jnnctqags73eqlmifjehq40vgeenj5i7.apps.googleusercontent.com',
        scopes: 'https://www.googleapis.com/auth/calendar.readonly',
      }).then(() => {
        const auth2 = gapi.auth2.init({
          clientId: '584697159634-jnnctqags73eqlmifjehq40vgeenj5i7.apps.googleusercontent.com',
        }).then(auth => {
          gapi.auth2.getAuthInstance().isSignedIn.listen(this.updateSigninStatus);
          this.updateSigninStatus(gapi.auth2.getAuthInstance().isSignedIn.get())
          this.fetchUpcomingEvents()
        })

      })
      
    },

    handleAuthClick() {
      gapi.auth2.getAuthInstance().signIn()
    },

    handleSignoutClick() {
      gapi.auth2.getAuthInstance().signOut()
    },

    updateSigninStatus(isSignedIn) {
      this.signedIn = isSignedIn
    },

    fetchUpcomingEvents() {
console.log('fetchingupcomingevents');
      gapi.client.calendar.events.list({
        'calendarId': 'vk9vndcse1sadqbi0fc730s83s@group.calendar.google.com',
        'timeMin': (new Date()).toISOString(),
        'showDeleted': false
      }).then(response => {
        this.upcomingEvents = response.result.items
      }).catch(err => console.dir(err))
    },
  },


  mounted() {
    if (!window.gapi) { 
      throw new Error('Could not load Google API object')
    } else {
      this.handleClientLoad()
    }
  },

  data () {
    return {
      signedIn: false,
      upcomingEvents: [],
    }
  }
}
</script>

<style>
</style>
