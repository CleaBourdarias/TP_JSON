<template>
  <div class="home">
    <h2>Villes par pays</h2>
    <div class="form-group">
            <label id="country" for="country"></label>
            <select id="countrySelected" class="form-control" @input="fetchCities">
              <option selected disabled hidden>Choisissez un pays</option>
              <option
                v-for="country in data.allCountries"
                :key="country.id"
                :value="country._links.cities.href"
              >
                {{ country.name }}
              </option>
            </select>
    </div>

    <div>
    <CitiesForCountry v-bind:cities="data.allCities"/>
    </div>
    
  </div>
</template>

<script setup>
import { reactive, onMounted } from "vue";
import CitiesForCountry from "@/components/CitiesForCountry.vue";

const data = reactive({
  allCountries: [],
  allCities: [],
});

function fetchCities() {
  // Utilise l'API ad-hoc pour avoir le pays de chaque ville
 
  fetch(document.getElementById("countrySelected").value)
    .then((response) => response.json())
    .then((json) => {
      data.allCities = [];
      let listeCities = json._embedded.cities 
      for(let i=0; i<listeCities.length;i++){
        data.allCities.push(listeCities[i]);
      }
      
      console.log(data.allCities);
    })
    .catch((error) => console.log());
}

// Utilise l'API REST auto-générée pour avoir les pays
function fetchCountries() {
  fetch("api/countries")
    .then((response) => response.json())
    .then((json) => {
      console.log(json._embedded.countries)
      data.allCountries = json._embedded.countries;
    })
    .catch((error) => console.log());
}

// Au chargement du composant
onMounted(() => {
  fetchCities(); // On récupère les villes (pour la table)
  fetchCountries(); // On récupère les pays (pour le sélecteur de pays)
});
</script>
