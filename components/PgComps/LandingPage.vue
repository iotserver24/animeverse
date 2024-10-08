<script setup>
import { useStorage } from "@vueuse/core";
const env = useRuntimeConfig();

const history_state = useStorage("site-watch", {});

const getSeason = () => {
  const today = new Date();
  const month = today.getMonth();
  const seasons = {
    0: "Winter",
    1: "Winter",
    2: "Spring",
    3: "Spring",
    4: "Summer",
    5: "Summer",
    6: "Fall",
    7: "Fall",
    8: "Fall",
    9: "Fall",
    10: "Winter",
    11: "Winter",
  };
  return seasons[month].toUpperCase();
};

const {
  data: trendingData,
  pending: trpend,
  refresh: trenddataRefresh,
  error: trenderr,
} = useFetch(
  `${env.public.API_URL}/api/${env.public.version}/trending?limit=12`,
  {
    cache: "force-cache",
  }
);
const {
  data: popularData,
  pending: popend,
  refresh: popdataRefresh,
  error: poperr,
} = useFetch(
  `${env.public.API_URL}/api/${env.public.version}/popular?limit=12`,
  {
    cache: "force-cache",
  }
);

// const {
//   data: scheduleData,
//   pending: schpend,
//   refresh: scheduledataRefresh,
//   error: scheduleerr,
// } = useFetch(
//   `${env.public.API_URL}/api/${env.public.version}/schedule?limit=12`,
//   {
//     cache: "force-cache",
//   }
// );

const {
  data: seasonData,
  pending: seaspend,
  refresh: seasdataRefresh,
  error: seaserr,
} = useFetch(
  `${env.public.API_URL}/api/${
    env.public.version
  }/season/${getSeason()}/${new Date().getFullYear()}?limit=12`,
  {
    cache: "force-cache",
  }
);
</script>

<template>
  <!-- eslint-disable vue/no-v-html -   class="d-none d-md-block"
    hide-delimiters-->
  <v-no-ssr>
   <v-carousel
  hide-delimiters
  progress="green"
  height="320px"
  :show-arrows="false"
  :cycle="true"
>
  <v-carousel-item
    v-for="(item, i) in trendingData?.results"
    :key="i"
    :src="item.bannerImage"
    cover
  >
    <div class="carousel-item">
      <img :src="item.coverImage.large" alt="Carousel Image" />
      <div class="d-flex flex-column pa-2 justify-center">
        <h3>{{ item.title.userPreferred }}</h3>
        <div
          style="
            overflow: hidden;
            transition: color 0.2s ease;
            display: -webkit-box;
            -webkit-box-orient: vertical;
            -webkit-line-clamp: 2;
          "
          v-html="item.description"
        />
        <div class="pt-2">
          <v-btn
            :to="'/pwa/anime/' + item.id"     
            :color="
              item.coverImage.color ? item.coverImage.color : 'transparent'
            "
            append-icon="mdi-open-in-new"
          >
            Read more
          </v-btn>
        </div>
      </div>
    </div>
  </v-carousel-item>
</v-carousel>

  </v-no-ssr>
  <!-- Search&History -->
  <v-container>
    <!--<SearchBar /> -->
    <ClientOnly>
      <div v-if="history_state?.latest_anime_watched">
        <v-alert
          class="mt-4"
          icon="mdi-history"
          title="Continue Watching : "
          :text="`${history_state?.latest_anime_watched?.title} Episode ${
            history_state?.latest_anime_watched?.curr_ep
          } ${history_state?.latest_anime_watched?.isDub ? 'Dub' : 'Sub'}`"
          closable
        >
          <template #default>
            <br />
            <v-btn
              class="my-2"
              :to="
                /\/pwa\.*/.test(useRoute().path)
                  ? `/pwa/watch/${history_state?.latest_anime_watched?.id}-${history_state?.latest_anime_watched?.ep_id}`
                  : `/watch/${history_state?.latest_anime_watched?.id}-${history_state?.latest_anime_watched?.ep_id}`
              "
              prepend-icon="mdi-play"
            >
              Resume?
            </v-btn>
          </template>
        </v-alert>
      </div>
    </ClientOnly>
  </v-container>
  <!-- DESKTOP DEVICE -->
  <v-container class="d-lg-block d-sm-none d-none" fluid>
    <v-col>
      <h1>Trending Anime</h1>
      <div v-if="trpend" class="loadingBlock">
        <v-progress-circular :size="45" indeterminate />
      </div>
      <div v-else-if="trenderr">
        <v-alert
          dense
          type="error"
          title="Error"
          text="Error loading trending anime!"
        />
        <v-btn @click="trenddataRefresh()">
          Reload?
          <v-icon>mdi-reload</v-icon>
        </v-btn>
      </div>
      <v-container v-else fluid>
        <div class="grid">
          <div
            v-for="(d, i) in trendingData?.results"
            :key="i"
            class="d-flex justify-center"
          >
            <AnimeCard
              :id="d.id"
              :title="d.title.userPreferred"
              :imgsrc="d.coverImage.large"
              :imgalt="d.id.toString()"
              :anime-color="d.coverImage.color"
              :year="d.seasonYear"
              :type="d.format"
              :total-ep="d.episodes"
              :status="d.status"
            />
          </div>
        </div>
      </v-container>
    </v-col>
    <v-col>
      <h1>Upcoming Anime : {{ getSeason() }}</h1>
      <div v-if="seaspend" class="loadingBlock">
        <v-progress-circular :size="45" indeterminate />
      </div>
      <div v-else-if="seaserr">
        <v-alert
          dense
          type="error"
          title="Error"
          text="Error loading season anime!"
        />
        <v-btn @click="seasdataRefresh()">
          Reload?
          <v-icon>mdi-reload</v-icon>
        </v-btn>
      </div>
      <v-container v-else fluid>
        <div class="grid">
          <div
            v-for="(d, i) in seasonData?.results"
            :key="i"
            class="d-flex justify-center"
          >
            <AnimeCard
              :id="d.id"
              :title="d.title.userPreferred"
              :imgsrc="d.coverImage.large"
              :anime-color="d.coverImage.color"
              :year="d.seasonYear"
              :type="d.format"
              :total-ep="d.episodes"
              :status="d.status"
            />
          </div>
        </div>
      </v-container>
    </v-col>
    <v-col>
      <h1>Popular Anime</h1>
      <div v-if="popend" class="loadingBlock">
        <v-progress-circular :size="45" indeterminate />
      </div>
      <div v-else-if="poperr">
        <v-alert
          dense
          type="error"
          title="Error"
          text="Error loading popular anime!"
        />
        <v-btn @click="popdataRefresh()">
          Reload?
          <v-icon>mdi-reload</v-icon>
        </v-btn>
      </div>
      <v-container v-else fluid>
        <div class="grid">
          <div
            v-for="(d, i) in popularData?.results"
            :key="i"
            class="d-flex justify-center"
          >
            <AnimeCard
              :id="d.id"
              :title="d.title.userPreferred"
              :imgsrc="d.coverImage.large"
              :anime-color="d.coverImage.color"
              :year="d.seasonYear"
              :type="d.format"
              :total-ep="d.episodes"
              :status="d.status"
            />
          </div>
        </div>
      </v-container>
    </v-col>
  </v-container>
  <!-- MOBILE DEVICE -->
  <v-container class="d-lg-none d-sm-block d-xs mb-5" fluid>
    <h2>Trending Anime</h2>
    <div v-if="trpend" class="loadingBlock">
      <v-progress-circular :size="45" indeterminate />
    </div>
    <div v-else-if="trenderr">
      <v-alert
        dense
        type="error"
        title="Error"
        text="Error loading trending anime!"
      />
      <v-btn @click="trenddataRefresh()">
        Reload?
        <v-icon>mdi-reload</v-icon>
      </v-btn>
    </div>
    <v-row v-else>
      <v-col class="media-scrolling">
        <div v-for="d in trendingData?.results" :key="d.id">
          <AnimeCard
            :id="d.id"
            :title="d.title.userPreferred"
            :imgsrc="d.coverImage.large"
            :anime-color="d.coverImage.color"
            :year="d.seasonYear"
            :type="d.format"
            :total-ep="d.episodes"
            :status="d.status"
          />
        </div>
      </v-col>
    </v-row>
    <h2 class="mt-10">Upcoming Anime : {{ getSeason() }}</h2>
    <div v-if="seaspend" class="loadingBlock">
      <v-progress-circular :size="45" indeterminate />
    </div>
    <div v-else-if="seaserr">
      <v-alert
        dense
        type="error"
        title="Error"
        text="Error loading season anime!"
      />
      <v-btn @click="seasdataRefresh()">
        Reload?
        <v-icon>mdi-reload</v-icon>
      </v-btn>
    </div>
    <v-row v-else>
      <v-col class="media-scrolling">
        <div v-for="d in seasonData?.results" :key="d.id">
          <AnimeCard
            :id="d.id"
            :title="d.title.userPreferred"
            :imgsrc="d.coverImage.large"
            :anime-color="d.coverImage.color"
            :year="d.seasonYear"
            :type="d.format"
            :total-ep="d.episodes"
            :status="d.status"
          />
        </div>
      </v-col>
    </v-row>
    <h2 class="mt-10">Popular Anime</h2>
    <div v-if="popend" class="loadingBlock">
      <v-progress-circular :size="45" indeterminate />
    </div>
    <div v-else-if="poperr">
      <v-alert
        dense
        type="error"
        title="Error"
        text="Error loading trending anime!"
      />
      <v-btn @click="popdataRefresh()">
        Reload?
        <v-icon>mdi-reload</v-icon>
      </v-btn>
    </div>
    <v-row v-else>
      <v-col class="media-scrolling">
        <div v-for="d in popularData?.results" :key="d.id">
          <AnimeCard
            :id="d.id"
            :title="d.title.userPreferred"
            :imgsrc="d.coverImage.large"
            :anime-color="d.coverImage.color"
            :year="d.seasonYear"
            :type="d.format"
            :total-ep="d.episodes"
            :status="d.status"
          />
        </div>
      </v-col>
    </v-row>
  </v-container>
</template>

<style>
.loadingBlock {
  height: 200px;
  display: grid;
  place-items: center;
}

.grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
  grid-template-rows: repeat(2, 1fr);
  grid-column-gap: 0px;
  grid-row-gap: 0px;
}

.media-scrolling {
  --_spacer: 0.6rem;
  margin-left: 0.5rem;
  margin-right: 0.5rem;
  display: grid;
  grid-auto-flow: column;
  overflow-x: auto;
  overscroll-behavior-inline: contain;
  scroll-snap-type: inline mandatory;
  scroll-padding-inline: var(--_spacer, 1rem);
}

.media-scrolling > * {
  scroll-snap-align: start;
}

@media (min-width: 768px) {
  .grid {
    grid-template-columns: repeat(auto-fit, minmax(290px, 1fr));
  }
}

.carousel-item {
  background-color: rgba(0, 0, 0, 0.7);
  display: flex;
  justify-content: center;
  padding: 2.5rem;
  height: 320px;
  gap: 1rem;
}
</style>
