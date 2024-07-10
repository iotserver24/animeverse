<!-- eslint-disable vue/no-multiple-template-root -->
<template>
  <v-container>
    <v-breadcrumbs style="padding: 0; margin-bottom: 1rem">
      <template #prepend>
        <v-icon size="small" icon="mdi-home"></v-icon>
      </template>
     <!-- <v-breadcrumbs-item title="Home" to="/pwa" />
      <v-breadcrumbs-divider />
      <v-breadcrumbs-item title="More" />-->
    </v-breadcrumbs> 
    <div class="pb-4">
      <h1>More</h1>
    </div>
    <v-list lines="two">
      <v-list-item title="AnimeVerse app" subtitle="Mobile app:100.1.1">
        <template #prepend>
          <img
            src="/icon.png"
            style="display: flex; width: 100px; height: 100%"
          />
        </template>
      </v-list-item>
      <v-list-item title="About us" target="blank" href="/about" />
      <v-list-item
        title="YouTube"
        target="blank"
        href="https://youtube.com/@r3ap3redit?si=hhoYRR4invbBl-yF"
      />
       <!--<v-list-item
        title="Join our Updates Channel"
        target="blank"
        href="https://t.me/AVupdate"
      />-->
        
        
      <!-- Adding this closing tag to properly close the v-list-item -->
      <v-list-item
        title=" "
        target="blank"
        href="https://liberapay.com/R3AP3R_editz/donate"
      >
        <template #prepend>
          <img
            alt="Donate us to keep it running"
            src="https://liberapay.com/assets/widgets/donate.svg"
            style="display: flex; width: 100px; height: 100%"
          />
        </template>
      </v-list-item>
    </v-list>
  </v-container>
</template>
