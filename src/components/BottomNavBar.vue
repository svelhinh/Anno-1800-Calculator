<template>
  <v-flex xs12>
    selectedWorldID: {{selectedWorldID}},
    selectedSocialClassID: {{selectedSocialClassID}},
    selectedProductionChainID: {{selectedProductionChainID}},
    <!-- 
      The following element is needed to trigger the initial rendering of the computed property selectedProductionChain 
      The element does render but is not shown
    -->
    <p v-show="false">{{selectedProductionChain}}</p>

    <v-card height="56px">
      <v-bottom-nav :active.sync="selectedProductionChainID" :value="true" absolute dark>
        <v-btn
          color="primary"
          flat
          v-for="(chain, i) in selectedProductionChains"
          :key="i"
          :value="chain.id"
        >
          <span>{{ chain.name }}</span>
          <img :src="chain.img" :alt="chain.name + ' Image'">
        </v-btn>
      </v-bottom-nav>
    </v-card>

    <!-- Nav Bar: SOCIAL CLASS -->
    <v-card height="56px">
      <v-bottom-nav :active.sync="selectedSocialClassID" :value="true" absolute dark>
        <v-btn
          color="primary"
          flat
          v-for="(socialClass, i) in selectedSocialClasses"
          :key="i"
          :value="socialClass.id"
          @click="resetProductionChain(socialClass.id)"
        >
          <span>{{ socialClass.name }}</span>
          <img :src="socialClass.img" :alt="socialClass.name + ' Image'">
        </v-btn>
      </v-bottom-nav>
    </v-card>

    <!-- Nav Bar: WORLD -->
    <v-card height="55px">
      <v-bottom-nav :active.sync="selectedWorldID" :value="true" absolute dark>
        <v-btn
          color="primary"
          flat
          v-for="(world, i) in worlds"
          :key="i"
          :value="world.id"
          @click="resetSocialClass(world.id)"
        >
          <span>{{ world.name }}</span>
          <img :src="world.img" :alt="world.name + ' Image'">
        </v-btn>
      </v-bottom-nav>
    </v-card>
  </v-flex>
</template>

<script>
import worlds from "../data/worlds.json";
import socialClasses from "../data/socialClasses.json";
import productionChains from "../data/productionChain";

export default {
  name: "BottomNavBar",
  data() {
    return {
      // Init selection
      selectedWorldID: 1,
      selectedSocialClassID: 1,
      selectedProductionChainID: 1,

      /* Store data from JSON in component */
      // TODO Load these centrally and access this data e.g. via Vuex
      worlds: worlds,
      socialClasses: socialClasses,
      productionChains: productionChains.Production_Chain
    };
  },

  computed: {
    /**
     * Search production chain by ChainID.
     *
     * @return {Object} The selected Production Chain
     */
    selectedProductionChain() {
      let selectedSocialClassChains = this.selectedProductionChains;
      let chainID = this.selectedProductionChainID;
      let productionChain = {};

      Object.keys(selectedSocialClassChains).forEach(chain => {
        if (selectedSocialClassChains[chain].id == chainID) {
          productionChain = selectedSocialClassChains[chain];
        }
      });
      this.setProductionChain(productionChain);
      return productionChain;
    },

    /**
     * Returns a deep copy of all Production Chains
     * @return {Object} A JS Object with all production chain objects init
     */
    fetchAllProductionChains() {
      return JSON.parse(JSON.stringify(this.productionChains));
    },

    /**
     * Filter the social classes for the ones available in the selected world.
     *
     * @return {array} The social classes of the selected world.
     */
    selectedSocialClasses: function() {
      const socialClasses = Object.values(this.socialClasses);
      return socialClasses.filter(
        socialClass => socialClass.worldID === this.selectedWorldID
      );
    },

    /**
     * Filter the production chains for the ones available in the selected social class.
     *
     * @return {array} The production chains of the selected world and social class.
     */
    selectedProductionChains: function() {
      const productionChains = Object.values(this.productionChains);
      return productionChains.filter(
        chain => chain.socialClassID === this.selectedSocialClassID
      );
    }
  },
  methods: {
    /**
     * After changing the world, display the first social class.
     */
    resetSocialClass: function(worldID) {
      let selectedWorld = this.getWorldByID(worldID);
      this.selectedWorldID = selectedWorld.id;

      let selectedSocialClass = this.getSocialClassByID(
        selectedWorld.startingSocialClassID
      );
      this.selectedSocialClassID = selectedSocialClass.id;

      this.resetProductionChain(selectedSocialClass.id);
    },

    /**
     * After changing the social class, display the first production chain.
     */
    resetProductionChain: function(socialClassID) {
      let socialClass = this.getSocialClassByID(socialClassID);
      this.selectedProductionChainID = socialClass.firstProductionChain;
    },

    /**
     * @param {Object} productionChain
     * Changes the VueX State so every Component can access the current productionChain
     * This updates when this.selectedProductionChainID updates
     */
    setProductionChain(productionChain) {
      this.$store.commit("changeProductionChain", productionChain);
    },

    /**
     * @param {int} id
     * @return {Object} The selected World Object
     * Searches all worlds by their world id
     */
    getWorldByID(id) {
      const worlds = Object.values(this.worlds);
      let selectedWorld = worlds.filter(world => world.id === id)[0];
      return selectedWorld;
    },

    /**
     * @param {int} id
     * @return {Object} The selected Social Class Object
     * Searches all social classes by their social class id
     *
     */
    getSocialClassByID(id) {
      const socialClasses = Object.values(this.socialClasses);
      let selectedSocialClass = socialClasses.filter(
        socialClass => socialClass.id === id
      )[0];
      return selectedSocialClass;
    }
  }
};
</script>

<style scoped>
</style>