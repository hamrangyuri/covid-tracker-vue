<template>
  <main v-if="!loading">
    <DataTitle :text="title" :dataDate="dataDate" />
    <DataBoxes :stats="stats" />
    <CountrySelect @get-country="getCountryData" :countries="countries" />
    <button
      @click="clearData"
      v-if="stats.Country"
      class="bg-green-700 text-white p-3 mt-10 focus:outline-none hover:bg-green-600 rounded"
    >
      Clear Country
    </button>
  </main>

  <main class="flex flex-col align-center justify-center text-center" v-else>
    <div class="text-gray-500 text-3xl mt-10 mb-6">Fetching Data</div>
    <img :src="loadingImage" class="w-14 m-auto" />
  </main>
</template>

<script>
import DataTitle from "../components/DataTitle.vue";
import CountrySelect from "../components/CountrySelect.vue";
import DataBoxes from "../components/DataBoxes.vue";

export default {
  name: "Home",
  components: { DataTitle, DataBoxes, CountrySelect },
  data() {
    return {
      loading: true,
      title: "Global",
      dataDate: "",
      stats: "",
      countries: [],
      loadingImage: require("../assets/hourglass.gif"),
    };
  },
  methods: {
    async fetchCovidData() {
      const res = await fetch("https://api.covid19api.com/summary");
      const data = await res.json();
      return data;
    },
    async getCountryData(country) {
      this.stats = country;
      this.title = country.Country;
    },
    async clearData() {
      this.loading = true;
      const data = await this.fetchCovidData();
      this.title = "Global";
      this.stats = data.Global;
      this.loading = false;
    },
  },

  //gets called on load like useEffect
  async created() {
    const { Date, Global, Countries } = await this.fetchCovidData();
    // console.log(data);
    this.dataDate = Date;
    this.stats = Global;
    this.countries = Countries;
    this.loading = false;
  },
};
</script>
