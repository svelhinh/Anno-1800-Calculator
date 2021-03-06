<template>
  <div>
    <v-layout row wrap justify-center>
      <v-flex>
        <div class="inline" v-for="(soClass, i) in reqPopArray" :key="i">
          <v-flex xs1>
            <v-flex xs12>
              <v-avatar size="24">
                <img class="center" :src="getImage(soClass.image, 'icons')" :alt="soClass.name">
              </v-avatar>
            </v-flex>
            <div align-content-center class="center">
              <v-flex xs12>
                <p class="pb-0 mb-0">{{soClass.name}}: {{soClass.population}}</p>
              </v-flex>
            </div>
          </v-flex>
        </div>
      </v-flex>
    </v-layout>
  </div>
</template>

<script>
import { helperFunctionMixin } from "../helperFunctionMixin.js";
import { chainNodeMixin } from "./chainNodeMixin.js";
import { EventBus } from "../../EventBus.js";

export default {
  mixins: [chainNodeMixin, helperFunctionMixin],
  props: {
    chain: {
      type: Object
    }
  },
  data() {
    return {
      buildingQueue: [],
      requiredPopulation: {
        farmers: 0,
        workers: 0,
        artisans: 0,
        engineers: 0,
        investors: 0,
        jornaleros: 0,
        obreros: 0
      }
    };
  },

  created() {
    // Event get emitted when clicking on Add to Demands Button in Visual Production Chain
    EventBus.$on("addToDemands", () => {
      this.$store.commit("addBuildings", this.buildingQueue);
      this.$router.push({
        name: "resident-demands",
        query: {
          linkedFromChains: true,
          populationToAdd: this.requiredPopulation
        }
      });
    });
  },

  computed: {
    reqPopArray() {
      let filteredArray = [];

      if (this.requiredPopulation.farmers !== 0) {
        filteredArray.push({
          population: this.requiredPopulation.farmers,
          name: "Farmer",
          image: "workforce-farmers.webp"
        });
      }

      if (this.requiredPopulation.workers !== 0) {
        filteredArray.push({
          population: this.requiredPopulation.workers,
          name: "Worker",
          image: "workforce-workers.webp"
        });
      }
      if (this.requiredPopulation.artisans !== 0) {
        filteredArray.push({
          population: this.requiredPopulation.artisans,
          name: "Artisans",
          image: "workforce-artisans.webp"
        });
      }
      if (this.requiredPopulation.engineers !== 0) {
        filteredArray.push({
          population: this.requiredPopulation.engineers,
          name: "Engineers",
          image: "workforce-engineers.webp"
        });
      }
      if (this.requiredPopulation.investors !== 0) {
        filteredArray.push({
          population: this.requiredPopulation.investors,
          name: "Investors",
          image: "workforce-investors.webp"
        });
      }
      if (this.requiredPopulation.jornaleros !== 0) {
        filteredArray.push({
          population: this.requiredPopulation.jornaleros,
          name: "Jornaleros",
          image: "workforce-jornaleros.webp"
        });
      }
      if (this.requiredPopulation.obreros !== 0) {
        filteredArray.push({
          population: this.requiredPopulation.obreros,
          name: "Obreros",
          image: "workforce-obreros.webp"
        });
      }

      return filteredArray;
    },

    newWorld() {
      const worldID = this.$store.state.selectedWorldID;
      if (worldID === 1) {
        return false;
      } else {
        return true;
      }
    }
  },

  watch: {
    chain(newChain) {
      const chainNodeMixin = this;
      this.resetRequiredPopulation();
      this.buildingQueue = {};
      chainNodeMixin.iterateProductionChain(
        this.chain,
        rootElement => this.getPopulationReq(rootElement),
        element => this.getPopulationReq(element),
        false
      );
    }
  },

  methods: {
    getPopulationReq(element) {
      const helperFunctionMixin = this;
      const building = helperFunctionMixin.getBuildingByName(
        element.name,
        element.worldID
      );
      this.requiredPopulation.farmers +=
        building.maintenance.farmer * element.relativeAmount;
      this.requiredPopulation.workers +=
        building.maintenance.worker * element.relativeAmount;
      this.requiredPopulation.artisans +=
        building.maintenance.artisan * element.relativeAmount;
      this.requiredPopulation.engineers +=
        building.maintenance.engineer * element.relativeAmount;
      this.requiredPopulation.investors +=
        building.maintenance.investor * element.relativeAmount;
      this.requiredPopulation.jornaleros +=
        building.maintenance.jornaleros * element.relativeAmount;
      this.requiredPopulation.obreros +=
        building.maintenance.obreros * element.relativeAmount;

      // this.addBuildingToQueue(element.name);
    },

    resetRequiredPopulation() {
      this.requiredPopulation = {
        farmers: 0,
        workers: 0,
        artisans: 0,
        engineers: 0,
        investors: 0,
        jornaleros: 0,
        obreros: 0
      };
    },

    addBuildingToQueue(building) {
      this.buildingQueue.push(building);
    }
  }
};
</script>

<style>
img {
  height: 40%;
  width: 40%;
}
div.inline {
  float: left;
}

img.center {
  display: block;
  margin-left: auto;
  margin-right: auto;
  width: 100%;
  height: 100%;
}
</style>
