<template>
  <header class="sticky top-0 bg-weather-primary shadow-lg">
    <nav class="container flex flex-col sm:flex-row items-center gap-4 text-white py-6">
      <RouterLink :to="{ name: 'home' }">
        <div class="flex items-center gap-3">
          <i class="fa-solid fa-sun text-2xl"></i>
          <p class="text-2xl">The Local Weather</p>
        </div>
      </RouterLink>

      <div class="flex gap-3 flex-1 justify-end">
        <i class="fa-solid fa-circle-info text-xl hover:text-weather-secondary duration-150 cursor-pointer"
          @click="toggleModal"></i>
        <i class="fa-solid fa-plus text-xl hover:text-weather-secondary duration-150 cursor-pointer"
          @[dynamicAction]="addCity"></i>
        <i class="fa-solid fa-calendar-check text-xl hover:text-weather-secondary duration-150 cursor-pointer"
          @click="goToAllCities">
        </i>
      </div>

      <BaseModal :modal-active="modalActive" @close-modal="toggleModal">
        <div class="text-black">
          <h1 class="text-2xl mb-1">About:</h1>
          <p class="mb-4">
            The Local Weather allows you to track the current and
            future weather of cities of your choosing.
          </p>
          <h2 class="text-2xl">How it works:</h2>
          <ol class="list-decimal list-inside mb-4">
            <li>
              Search for your city by entering the name into the
              search bar.
            </li>
            <li>
              Select a city within the results, this will take
              you to the current weather for your selection.
            </li>
            <li>
              Track the city by clicking on the "+" icon in the
              top right. This will save the city to view at a
              later time on the home page.
            </li>
          </ol>

          <h2 class="text-2xl">Removing a city</h2>
          <p>
            If you no longer wish to track a city, simply select
            the city within the home page. At the bottom of the
            page, there will be am option to delete the city.
          </p>
        </div>
      </BaseModal>
    </nav>
  </header>
</template>

<script setup>
import { ref } from 'vue'
import { RouterLink, useRoute, useRouter } from "vue-router";
import BaseModal from "./BaseModal.vue";
import { uid } from 'uid';
import { useAppStore } from '../stores/appStore';

const router = useRouter();
const appStore = useAppStore()
const modalActive = ref(null);
const route = useRoute();
const dynamicAction = ref("click");

const toggleModal = () => {
  modalActive.value = !modalActive.value;
}

const addCity = () => {

  if (!appStore.currentLocation?.data?.current && appStore.locations.length < 1) {
    window.alert("Please search for a location before adding it");
  }
  else if (appStore.locations[0]?.data.location.name === appStore.search || appStore.locations[0]?.data.location.country === appStore.search) {
    window.alert(`${appStore.currentLocation.data.location.name} was already added`)
  }
  else {
    appStore.addLocation(appStore.currentLocation);
    window.alert(`${appStore.currentLocation?.data?.location.name} was added successfully`);
    appStore.changeCurrentLocation(null);
  }
}

const goToAllCities = () => {
  if (appStore.locations.length < 1) {
    window.alert("Please add atleast 1 location")
    return
  }
  router.push({
    name: 'allCitiesView'
  })
}

</script>
