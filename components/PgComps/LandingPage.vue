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

const {
  data: recentData,
  pending: recend,
  refresh: recdataRefresh,
  error: recerr,
} = useFetch(
  `${env.public.API_URL}/api/${env.public.version}/recent?limit=12`,
  {
    cache: "force-cache",
  }
);

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
      <h1>Recent Anime</h1>
      <div v-if="recend" class="loadingBlock">
        <v-progress-circular :size="45" indeterminate />
      </div>
      <div v-else-if="recerr">
        <v-alert
          dense
          type="error"
          title="Error"
          text="Error loading recent anime!"
        />
        <v-btn @click="recdataRefresh()">
          Reload?
          <v-icon>mdi-reload</v-icon>
        </v-btn>
      </div>
      <v-container v-else fluid>
        <div class="grid">
          <div
            v-for="(d, i) in recentData?.results"
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
  <v-container v-else fluid>
    <v-carousel
      class="hidden-md-and-up"
      cycle
      hide-delimiters
      height="300px"
      show-arrows
    >
      <v-carousel-item
        v-for="(d, i) in trendingData?.results"
        :key="i"
        class="carousel__item"
      >
        <v-img :src="d.coverImage.large" class="align-end" gradient="to bottom, rgba(0,0,0,0.1), rgba(0,0,0,0.5)">
          <template v-slot:placeholder>
            <v-row class="fill-height ma-0" align="center" justify="center">
              <v-progress-circular indeterminate color="grey lighten-5"></v-progress-circular>
            </v-row>
          </template>
          <v-container fill-height fluid class="pa-0">
            <v-row fill-height align="end" class="pa-0 ma-0">
              <v-col class="text-subtitle-1 white--text">
                {{ d.title.userPreferred }}
              </v-col>
            </v-row>
          </v-container>
        </v-img>
      </v-carousel-item>
    </v-carousel>
  </v-container>
  <h2>Recent Anime</h2>
  <div v-if="recend" class="loadingBlock">
    <v-progress-circular :size="45" indeterminate />
  </div>
  <div v-else-if="recerr">
    <v-alert
      dense
      type="error"
      title="Error"
      text="Error loading recent anime!"
    />
    <v-btn @click="recdataRefresh()">
      Reload?
      <v-icon>mdi-reload</v-icon>
    </v-btn>
  </div>
  <v-container v-else fluid>
    <v-carousel
      class="hidden-md-and-up"
      cycle
      hide-delimiters
      height="300px"
      show-arrows
    >
      <v-carousel-item
        v-for="(d, i) in recentData?.results"
        :key="i"
        class="carousel__item"
      >
        <v-img :src="d.coverImage.large" class="align-end" gradient="to bottom, rgba(0,0,0,0.1), rgba(0,0,0,0.5)">
          <template v-slot:placeholder>
            <v-row class="fill-height ma-0" align="center" justify="center">
              <v-progress-circular indeterminate color="grey lighten-5"></v-progress-circular>
            </v-row>
          </template>
          <v-container fill-height fluid class="pa-0">
            <v-row fill-height align="end" class="pa-0 ma-0">
              <v-col class="text-subtitle-1 white--text">
                {{ d.title.userPreferred }}
              </v-col>
            </v-row>
          </v-container>
        </v-img>
      </v-carousel-item>
    </v-carousel>
  </v-container>
  <h2>Popular Anime</h2>
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
    <v-carousel
      class="hidden-md-and-up"
      cycle
      hide-delimiters
      height="300px"
      show-arrows
    >
      <v-carousel-item
        v-for="(d, i) in popularData?.results"
        :key="i"
        class="carousel__item"
      >
        <v-img :src="d.coverImage.large" class="align-end" gradient="to bottom, rgba(0,0,0,0.1), rgba(0,0,0,0.5)">
          <template v-slot:placeholder>
            <v-row class="fill-height ma-0" align="center" justify="center">
              <v-progress-circular indeterminate color="grey lighten-5"></v-progress-circular>
            </v-row>
          </template>
          <v-container fill-height fluid class="pa-0">
            <v-row fill-height align="end" class="pa-0 ma-0">
              <v-col class="text-subtitle-1 white--text">
                {{ d.title.userPreferred }}
              </v-col>
            </v-row>
          </v-container>
        </v-img>
      </v-carousel-item>
    </v-carousel>
  </v-container>
</v-container>
</template>

<style scoped>
.carousel-item {
  display: flex;
  align-items: center;
  justify-content: center;
  flex-direction: column;
}
.carousel-item img {
  max-height: 200px;
}
.grid {
  display: flex;
  flex-wrap: wrap;
  gap: 1rem;
}
.grid > div {
  flex: 1 1 calc(25% - 1rem);
}
@media (max-width: 960px) {
  .grid > div {
    flex: 1 1 calc(50% - 1rem);
  }
}
@media (max-width: 600px) {
  .grid > div {
    flex: 1 1 100%;
  }
}
</style>
