<template>
  <v-app id="anno1800calc">
    <v-navigation-drawer v-model="drawer" fixed clipped class="grey lighten-4" app>
      <v-list dense class="grey lighten-4">
        <template v-for="(item, i) in items">
          <v-layout v-if="item.heading" :key="i" row align-center>
            <v-flex xs6>
              <v-subheader v-if="item.heading">{{ item.heading }}</v-subheader>
            </v-flex>
            <v-flex xs6 class="text-xs-right">
              <!-- <v-btn small flat>edit</v-btn> -->
            </v-flex>
          </v-layout>
          <v-divider v-else-if="item.divider" :key="i" dark class="my-3"></v-divider>
          <v-list-tile v-else :key="i" :to="{path: item.route}">
            <v-list-tile-action>
              <v-icon>{{ item.icon }}</v-icon>
            </v-list-tile-action>
            <v-list-tile-content>
              <v-list-tile-title class="grey--text">{{ item.text }}</v-list-tile-title>
            </v-list-tile-content>
          </v-list-tile>
        </template>
      </v-list>
    </v-navigation-drawer>
    <v-toolbar color="primary" app scroll-off-screen clipped-left>
      <v-toolbar-side-icon @click="drawer = !drawer"></v-toolbar-side-icon>
      <span class="title ml-3 mr-5">
        {{ title }} ||&nbsp;
        <span class="font-weight-light subheading">{{subtitle}}</span>
      </span>
    </v-toolbar>
    <v-content>
      <router-view></router-view>
    </v-content>
  </v-app>
</template>

<script>
import Producers from './data/producers.json';
export default {
  data: () => ({
    drawer: null,
    title: 'Anno 1800 Calculator',
    subtitle: 'a production chain cheatsheet',
    items: [
      { icon: 'home', text: 'Home', route: '/Home' },
      { divider: true },
      { heading: 'Production' },
      { icon: 'merge_type', text: 'Production Chains', route: '/chains' },
      { icon: 'trending_up', text: 'Resident Demand', route: '/demands' },
      { divider: true },
      { heading: 'Information' },
      {
        icon: 'list',
        text: 'Building Queue (WIP)',
        route: '/queue',
      },
      {
        icon: 'border_inner',
        text: 'Production Layouts (WIP)',
        route: '/layouts',
      },
      { divider: true },
      { icon: 'info', text: 'About', route: '/about' },
    ],
  }),
  props: {
    source: String,
  },

  mounted() {
    this.$nextTick(function() {
      const productionProducts = this.buildProductObject();
      const consumptionProducts = this.buildProductObject();
      // this.$store.commit( "buildProductObjects",productionProducts,consumptionProducts);
    });
  },

  methods: {
    buildProductObject() {
      const producerObject = {};
      const producerFile = JSON.parse(JSON.stringify(Producers.Producers));
      for (let i = 0; i < producerFile.length; i++) {
        const producerKey = producerFile[i];

        const productName = producerFile[producerKey].product;
        producerObject[productName] = 0;
      }
      // console.log(producerObject);
      return this.producerObject;
    },
  },
};
</script>

<style lang="stylus">
#keep {
  .v-navigation-drawer__border {
    display: none;
  }
}
</style>
