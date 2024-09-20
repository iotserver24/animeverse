<script setup>
const env = useRuntimeConfig();

const getID = useRoute().params.id.split("-")[0];
const getGogoID = useRoute().params.id.split(`${getID}-`)[1];
const getEP = getGogoID.split(`-episode-`)[1];

const dl_dialog = ref(false);

const { data: anime } = await useFetch(
  `${env.public.API_URL}/api/${env.public.version}/info/${getID}`,
  {
    cache: "force-cache",
  }
);

const { data: animedl, pending: aniDLpending } = useFetch(
  `${env.public.API_URL}/api/v1/download/${getGogoID}`,
  {
    cache: "force-cache",
  }
);

const { data: dlURL } = useFetch(
  `https://api.iotserver24.workers.dev/download/${getGogoID}`,
  {
    cache: "force-cache",
    responseType: "json",
  }
);

useHead({
  htmlAttrs: {
    lang: "en",
  },
  title: anime.value?.title.userPreferred
    ? "Download " + anime.value?.title.userPreferred + " Episode " + getEP
    : "animeverse", 
});
</script>

<template>
  <v-container>
    <v-dialog v-model="dl_dialog" max-width="500px" scrim="#191919">
      <v-card>
        <v-card-title>Download</v-card-title>
        <v-card-text class="d-flex flex-column ga-2">
          <v-btn
            prepend-icon="mdi-download"
            :href="dlURL.results['640x360']"
            color="red"
            :disabled="dlURL.results['640x360'] === ''"
          >
            360p
          </v-btn>
          <v-btn
            prepend-icon="mdi-download"
            :href="dlURL.results['854x480']"
            color="red"
            :disabled="dlURL.results['854x480'] === ''"
          >
            480p
          </v-btn>
          <v-btn
            prepend-icon="mdi-download"
            :href="dlURL.results['1280x720']"
            color="red"
            :disabled="dlURL.results['1280x720'] === ''"
          >
            720p (HD)
          </v-btn>
          <v-btn
            prepend-icon="mdi-download"
            :href="dlURL.results['1920x1080']"
            color="red"
            :disabled="dlURL.results['1920x1080'] === ''"
          >
            1080p (FHD)
          </v-btn>

         
            <em class="pb-4 d-block" style="color: red">
            made by: R3AP3R editz
            <!--<a href="https://api.iotserver24.workers.dev/">iotserver24</a>-->
          </em>
        </v-card-text>
        <v-card-actions>
          <v-btn prepend-icon="mdi-close" @click="dl_dialog = false"></v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
    <v-sheet
      elevation="12"
      max-width="600"
      rounded="lg"
      width="100%"
      class="ma-2 pa-4 text-center mx-auto"
    >
      <v-icon class="mb-5" color="blue" icon="mdi-download" size="112" />
      <h2 class="mb-6">Download</h2>
      <h3 class="mb-2">
        {{ anime?.title.userPreferred + " Episode " + getEP }}
        <br />
      </h3>
      <!--<em class="pb-4 d-block" style="color: red">
        *This will redirected to Anitaku download page!
      </em> -->
      <v-divider class="mb-4" />
      <div class="text-end ga-2 d-flex flex-row-reverse">
        <v-btn
          :to="'/watch/' + useRoute().params.id"
          prepend-icon="mdi-play"
          color="red"
        >
          Watch
        </v-btn>
        <v-btn color="blue" prepend-icon="mdi-link" @click="dl_dialog = true">
          Download
        </v-btn>
      </div>
    </v-sheet>
  </v-container>
</template>
