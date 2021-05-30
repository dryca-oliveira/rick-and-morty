<template>
  <v-app id="app">
    <v-app-bar
      app
      elevate-on-scroll
      color="grey darken-4"
      scroll-off-screen
      dark
    >
      <div class="d-flex align-center">
        <v-img
          alt="Vuetify Name"
          class="shrink mb-5 hidden-sm-and-down mr-0"
          contain
          min-width="390"
          src="./assets/logo1.png"
          position="center left"
          width="300"
          height="90"
        />
      </div>
      <v-snackbar v-model="snackbar">
        Não encontrado
        <template v-slot:action="{ attrs }">
          <v-btn color="white" text v-bind="attrs" @click="snackbar = false">
            Fechar
          </v-btn>
        </template>
      </v-snackbar>

      <v-spacer></v-spacer>
      <v-responsive max-width="250">
        <v-text-field
          class="pt-5"
          single-line
          v-model="name"
          @keyup.enter="searchCharacter()"
          color="white"
          label="Pesquisar personagem"
        ></v-text-field>
      </v-responsive>

      <v-btn class="pt-2" icon @click="searchCharacter()">
        <v-icon>mdi-magnify</v-icon>
      </v-btn>

      <template>
        <v-navigation-drawer v-model="drawer" absolute app temporary dark>
          <v-list dense>
            <v-list-item-group>
              <v-list-item
                v-for="(item, index) in characters"
                :key="index"
                @click="selectItem(item)"
              >
                <v-list-item-title>{{ item.name }}</v-list-item-title>
              </v-list-item>
            </v-list-item-group>
          </v-list>
        </v-navigation-drawer>
      </template>
    </v-app-bar>

    <v-main class="background">
      <v-container class="py-2 my-12 container" fluid>
        <v-row align="center" justify="center">
          <transition name="fadeOut">
            <v-col v-if="card" sm="10" md="5" xl="3">
              <CharacterCard :characterInfo="character" />
            </v-col>
          </transition>
          <transition name="zoomOut">
            <v-col v-if="portal" sm="10" md="5" xl="3">
              <Portal />
            </v-col>
          </transition>
        </v-row>
      </v-container>
    </v-main>
  </v-app>
</template>

<script>
import Portal from "./components/Portal";
import CharacterCard from "./components/CharacterCard";

export default {
  name: "App",

  components: {
    Portal,
    CharacterCard,
  },

  data: () => ({
    characters: [],
    search: "",
    character: {},
    name: "",
    drawer: false,
    group: null,
    card: false,
    portal: true,
    snackbar: false,
  }),

  methods: {
    async searchCharacter() {
      this.card = false;
      if (this.name.length > 1) {
        await this.$http
          .get(`character/?name=${this.name}`)
          .then((resp) => {
            this.characters = resp.data.results;
            this.drawer = true;
            setTimeout(() => {
              this.portal = true;
            }, 500);
          })
          .catch((error) => {
            console.log(error);
            this.snackbar = true;
          });
      } else {
        this.name = "";
        setTimeout(() => {
          this.portal = true;
        }, 800);
      }
    },
    selectItem(item) {
      this.drawer = false;
      this.character = item;
      this.portal = false;
      setTimeout(() => {
        this.card = true;
      }, 800);
    },
  },
};
</script>
<style>
#app {
  overflow-x: hidden;
}
.background {
  background-image: url("./assets/universo.jpg");
  background-size: cover;
}
.container {
  overflow: hidden;
}
.zoomOut-leave-active {
  transition: all 1s;
}
.zoomOut-leave-to {
  transform: rotate(-360deg) scale(0, 0);
  opacity: 0;
}
.fadeOut-leave-active {
  transition: all 0.5s;
}
.fadeOut-leave-to {
  transform: translateX(2040px);
  opacity: 0;
}
</style>
