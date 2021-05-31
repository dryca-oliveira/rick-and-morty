<template>
  <v-card dark class="animation card" min-height="352" width="620px">
    <v-card-title
      style="background-color: #000"
      class="headline white--text title text-md-h5 text-lg-h4"
      >{{ characterInfo.name }}
    </v-card-title>
    <v-row>
      <v-col
        cols="12"
        md="6"
        class="pl-10 pb-0 subtitle-2 text-lg-subtitle-1 infos"
      >
        <div class="py-2 d-flex">
          <v-icon :color="color" small>mdi mdi-checkbox-blank-circle </v-icon>
          <span class="px-1"> Status: </span> {{ characterInfo.status }}
        </div>
        <div class="py-2">
          <span>Especie: </span>{{ characterInfo.species }}
        </div>
        <div class="py-2"><span>Genero: </span>{{ characterInfo.gender }}</div>
        <div class="py-2">
          <span>Primeira localização: </span>{{ characterInfo.origin.name }}
        </div>
        <div class="py-2">
          <span>Última localização: </span>{{ characterInfo.location.name }}
        </div>
      </v-col>
      <v-col class="pb-0" cols="12" md="6"
        ><v-img
          :src="characterInfo.image"
          :lazy-src="characterInfo.image"
          :alt="characterInfo.name"
        >
          <template v-slot:placeholder>
            <v-row class="fill-height ma-0" align="center" justify="center">
              <v-progress-circular
                indeterminate
                color="grey lighten-5"
              ></v-progress-circular>
            </v-row>
          </template>
        </v-img>
      </v-col>
    </v-row>
    <v-row>
      <v-col class="pt-0">
        <v-expansion-panels>
          <v-expansion-panel>
            <v-expansion-panel-header class="orange">
              <span>Episódios: {{ characterInfo.episode.length }} </span>
            </v-expansion-panel-header>
            <v-expansion-panel-content>
              <template v-if="characterInfo.episode.length > 1">
                <span v-for="(item, index) in names" :key="index">
                  {{ item }},
                </span>
              </template>
              <template v-else>
                <span>
                  {{ names }}
                </span>
              </template>
            </v-expansion-panel-content>
          </v-expansion-panel>
        </v-expansion-panels>
      </v-col>
    </v-row>
  </v-card>
</template>

<script>
export default {
  props: {
    characterInfo: Object,
  },
  data() {
    return {
      episodesId: [],
      episodes: [],
      color: "grey",
      names: [],
    };
  },
  mounted() {
    this.updateCard();
    this.setColor();
  },
  methods: {
    async updateCard() {
      let ids;
      this.characterInfo.episode.forEach((element) => {
        let id = element.substring(40);
        this.episodesId.push(id);
      });
      ids = this.episodesId.join(",");
      await this.$http
        .get("episode/" + ids)
        .then((resp) => {
          this.episodes = resp.data;
          if (this.episodes.length > 1) {
            this.episodes.forEach((element) => {
              this.names.push(element.name);
            });
          } else {
            this.names = this.episodes.name;
          }
        })
        .catch((error) => {
          console.log(error);
        });
    },
    setColor() {
      if (this.characterInfo.status == "Alive") {
        this.color = "blue";
      }
      if (this.characterInfo.status == "Dead") {
        this.color = "red";
      }
    },
  },
};
</script>

<style scoped>
.animation {
  animation: fadeInRight 2.5s ease;
  overflow: hidden;
}
@keyframes fadeInRight {
  0% {
    opacity: 0;
    transform: translateX(2040px);
  }
  100% {
    opacity: 1;
    transform: translateX(0);
  }
}
.card {
  box-shadow: 0px 0px 15px rgba(86, 211, 211, 0.842) !important;
}
.title {
  font-family: "Sigmar One", cursive !important;
}
.infos {
  font-weight: 300;
}
.infos span {
  color: rgb(116, 113, 113);
  font-weight: bold;
}
</style>