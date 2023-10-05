<template>
  <div>
    <h1>Profile</h1>
    <v-card>
      <v-card-title>Contact Information</v-card-title>
      <v-img src="https://scontent.fmex38-1.fna.fbcdn.net/v/t39.30808-6/361860328_6325981934175502_1781630144867929471_n.jpg?_nc_cat=104&ccb=1-7&_nc_sid=a2f6c7&_nc_eui2=AeEJSYhHxYDtMn4n0Mog1KqrdocQHxxo9SZ2hxAfHGj1JjoqRCOIMbZZ8xCH676Edqv0Jo4w4VsHEX66HrieMqH0&_nc_ohc=tV54zNsNchgAX8d6TxB&_nc_ht=scontent.fmex38-1.fna&oh=00_AfA3y1-CI9hKEGGIAWFe-l5anptX1vHF3jWG_UYqCdqsqg&oe=65234E30" aspect-ratio="1" style="max-width: 15%"></v-img>
      <v-card-text>
        Name: {{ profile.name }}<br>
        Email: {{ profile.email }}<br>
        Phone: {{ profile.phone }}<br>
        Facebook: {{ profile.link }}
      </v-card-text>
    </v-card>
  </div>
</template>

<script>
export default {
  name: 'ProfilePage',
  data() {
    return {
      profile: {
        name: 'Bryan Jésus Suárez Nieto',
        email: 'fenrer_wow@hotmail.com',
        phone: '56 1673 3029',
        link: 'https://www.facebook.com/bryan.suareznieto'
      },
    }
  },
}
</script>
