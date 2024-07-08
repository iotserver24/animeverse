<script setup>
useSeoMeta({
  ogTitle: "About",
  ogDescription: "About AnimeVerse...",
  ogImage: "logo.png",
  ogUrl: "[og:url]",
  twitterTitle: "About",
  twitterDescription: "About AnimeVerse...",
  twitterImage: "logo.png",
  twitterCard: "summary",
});

useHead({
  htmlAttrs: {
    lang: "en",
  },
  title: "About",
});
</script>
<template>
  <v-container>
    <div style="height: fit-content; display: grid; place-items: center">
      <div class="pa-5" style="background-color: #212121">
        <h1><img src="/logo.png" alt="loho" height="50" /></h1>
        <p>
          AnimeVerse is an anime streaming platform that offer free
          anime with and install it as Android app or IOS web app. <br />
          Build with Nuxt 3, Vuetify and AnimeVerse's V2 API.
        </p>
       
        
        <!-- <script src="https://liberapay.com/R3AP3R_editz/widgets/button.js"></script>
<noscript><a href="https://liberapay.com/R3AP3R_editz/donate"><img alt="Donate using Liberapay" src="https://liberapay.com/assets/widgets/donate.svg"></a></noscript>
         -->
          
       
        <div class="my-5">
          <h1>Donation</h1>
          We do not use ADs as from ADs I could only earn 1-2$ a month while server cost is 10$ a months and rely on donation to keep the website running forever. 
          You can consider donating us to keep it alive forever!! <br>
          <v-btn
            class="mt-4"
            color="yellow-darken-1"
            prepend-icon="mdi-cash"
            href="https://liberapay.com/R3AP3R_editz/"
          >
            Donation
          </v-btn>
        </div>
         <div class="my-5">
      <h1>About Me:</h1>
      <p>Hello, I am <strong>R3AP3R editz</strong> (Anish Kumar).</p>
      <p>I love watching anime and making anime edits. I have a passion for creating content that anime fans will enjoy and find engaging.</p>
      <p>I also run a YouTube channel, <strong>R3AP3R editz</strong>, where I regularly upload my latest anime edits.</p>
      <p> I have programmed and developed this <strong>AnimeVerse</strong>, a platform where you can watch anime for free. This project is a culmination of my love for anime and my skills in programming.</p>
      <p>I hope you enjoy using AnimeVerse as much as I enjoyed creating it. Thank you for being a part of this journey!</p>
      </div>
     </div>
    </div>
  </v-container>
</template>
