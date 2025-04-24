<template>
  <div class="col-7" v-if="country">
    <img :src="flagUrl" alt="Flag" style="width: 100px" />
    <h1>{{ country.name.common }}</h1>
    <table class="table">
      <tbody>
        <tr>
          <td style="width: 30%">Capital</td>
          <td>{{ country.capital[0] }}</td>
        </tr>
        <tr>
          <td>Area</td>
          <td>{{ country.area }} km <sup>2</sup></td>
        </tr>
        <tr>
          <td>Borders</td>
          <td>
            <ul>
              <li v-if="country.borders.length === 0">No borders available</li>
              <li v-for="border in country.borders" :key="border">
                <router-link
                  :to="{
                    name: 'country-details',
                    params: { alpha3Code: border },
                  }"
                >
                  {{ getCountryName(border) }}
                </router-link>
              </li>
            </ul>
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script setup>
import { ref, onMounted, computed, watch } from "vue";
import { useRoute } from "vue-router";

const route = useRoute();
const country = ref(null);
const countriesMap = ref({});

// Function to get country name from alpha-3 code
const getCountryName = (alpha3Code) => {
  return countriesMap.value[alpha3Code] || alpha3Code; // Fallback to alpha-3 code if not found
};

const flagUrl = computed(() => {
  return country.value
    ? `https://flagpedia.net/data/flags/icon/72x54/${country.value.alpha2Code.toLowerCase()}.png`
    : "";
});

const getAllCountries = async () => {
  try {
    const countriesResponse = await fetch(
      `https://ih-countries-api.herokuapp.com/countries`
    );
    const countriesData = await countriesResponse.json();
    countriesMap.value = countriesData.reduce((map, country) => {
      map[country.alpha3Code] = country.name.common; // Mapping alpha-3 code to country name
      return map;
    }, {});
  } catch (error) {
    console.error(error);
  }
};

const getOneCountry = async (countryCode) => {
  try {
    const response = await fetch(
      `https://ih-countries-api.herokuapp.com/countries/${countryCode}`
    );
    country.value = await response.json();
    
  } catch (error) {
    console.error(error)
  }
}

onMounted(async () => {
  try {
    await getAllCountries()
    await getOneCountry(route.params.alpha3Code)
    
    watch(
      () => {
        return route.params.alpha3Code
      },
      (newValue) => {
        getOneCountry(newValue)
      }
    )


    // Debugging: Log the entire country object
    console.log("Country Data:", country.value); // Check the structure and borders
  } catch (error) {
    console.error("Error fetching country data:", error);
  }
});

</script>

<style>
/* Add specific styles here */
</style>
