<!-- src/components/CountriesList.vue -->
<template>
  <div class="row">
    <div class="col-5" style="max-height: 90vh; overflow: scroll">
      <div class="list-group">
        <router-link
          v-for="country in countries"
          :key="country.alpha3Code"
          :to="{
            name: 'country-details',
            params: { alpha3Code: country.alpha3Code },
          }"
          class="list-group-item list-group-item-action"
        >
          <img
            :src="getFlagUrl(country.alpha2Code)"
            alt="Flag"
            class="flag-icon"
          />
          <p>{{ country.name.common }}</p>
        </router-link>
      </div>
    </div>
    <router-view />
  </div>
</template>

<script setup>
import { ref, onMounted } from "vue";

const countries = ref([]);

onMounted(async () => {
  const response = await fetch(
    "https://ih-countries-api.herokuapp.com/countries"
  );
  countries.value = await response.json();
});

const getFlagUrl = (alpha2Code) => {
  return `https://flagpedia.net/data/flags/icon/72x54/${alpha2Code.toLowerCase()}.png`;
};
</script>

<style>
.flag-icon {
  width: 72px;
  height: auto;
  margin-right: 10px;
}
</style>
