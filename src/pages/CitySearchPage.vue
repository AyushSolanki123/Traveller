<template>
  <div>
    <transition
      appear
      enter-active-class="animated backInLeft"
      leave-active-class="animated backOutLeft"
    >
      <WeatherData
        :search="this.search"
        :showBanner="showBanner"
        :weatherInfo="this.weatherInfo[0]"
        @closeBanner="showBanner = false"
      />
    </transition>
    <q-page class="constrain q-pa-md">
      <div class="row q-gutter-xl justify-center">
        <q-btn
          color="white"
          text-color="black"
          outline
          icon-right="hotel"
          :ripple="{ color: 'blue' }"
          label="Hotels"
          @click="getHotels"
        />

        <q-btn
          color="white"
          text-color="black"
          outline
          :ripple="{ color: 'blue' }"
          icon-right="restaurant"
          label="Restaurant"
          @click="getRestaurants"
        />
        <q-btn
          color="white"
          text-color="black"
          outline
          :ripple="{ color: 'blue' }"
          icon-right="tour"
          label="Activites"
          @click="getAttractions"
        />
        <q-btn
          color="white"
          text-color="black"
          outline
          icon-right="mail"
          :ripple="{ color: 'blue' }"
          label="Suggestions"
          @click="showRoute = true"
        />
      </div>
      <template v-if="!loadingPlaces">
        <q-card flat v-if="searchData[0].is_top_result">
          <q-card-section class="text-h2 text-bold q-px-none">
            <span class="text-orange-6">Explore</span>
            {{ search }}
          </q-card-section>
          <div class="row no-wrap">
            <div>
              <q-card-section class="q-pa-sm">
                <img
                  style="height: 505px; width: 500px"
                  :src="images[0].images.large.url"
                  alt=""
                />
              </q-card-section>
            </div>
            <div class="column">
              <div v-for="i in 2" :key="i">
                <q-card-section class="q-pa-sm">
                  <img
                    style="height: 250px; width: 250px"
                    :src="images[i].images.medium.url"
                    alt=""
                  />
                </q-card-section>
              </div>
            </div>
          </div>
        </q-card>
        <div class="row">
          <div
            class="col-3 q-ma-sm"
            v-for="searches in searchData"
            :key="searches.id"
          >
            <div v-if="!searches.is_top_result" class="q-pt-md">
              <q-card class="mycard">
                <div v-if="searches.result_object.photo">
                  <q-img
                    style="height: 200px"
                    :src="searches.result_object.photo.images.small.url"
                  />
                </div>
                <div v-else>
                  <q-img src="https://i.imgur.com/IIvismu.jpeg" />
                </div>
                <q-card-section>
                  <div class="row no-wrap">
                    <div class="col text-h6 ellipsis">
                      {{ searches.result_object.name }}
                    </div>
                  </div>
                  <div class="text-subtitle1 text-caption">
                    {{ searches.result_object.address }}
                  </div>
                  <div>
                    <q-rating
                      :value="parseInt(searches.result_object.rating, 10)"
                      :max="5"
                      size="32px"
                    />
                  </div>
                </q-card-section>

                <q-card-section class="q-pt-none"> </q-card-section>
              </q-card>
            </div>
          </div>
        </div>
      </template>
      <template v-else>
        <q-card flat>
          <q-card-section class="text-h2 text-bold q-px-none">
            <tr>
              <th>
                <span class="text-orange-6">Explore </span>
              </th>
              <th class="text-right" style="width: 400px">
                <q-skeleton type="text" />
              </th>
            </tr>
          </q-card-section>
          <div class="row no-wrap">
            <div>
              <q-card-section class="q-pa-sm">
                <q-skeleton height="505px" width="500px" square />
              </q-card-section>
            </div>
            <div class="column">
              <div v-for="i in 2" :key="i">
                <q-card-section class="q-pa-sm">
                  <q-skeleton height="250px" width="250px" square />
                </q-card-section>
              </div>
            </div>
          </div>
        </q-card>
        <div class="row">
          <div class="col-3 q-ma-sm" v-for="i in 3" :key="i">
            <div class="q-pt-md" v-for="j in 3" :key="j">
              <q-card class="mycard">
                <div>
                  <q-skeleton width="100%" height="200px" square />
                </div>

                <q-card-section class="p-mt-xl">
                  <div class="row no-wrap">
                    <div class="col text-h6 ellipsis">
                      <q-skeleton square />
                    </div>
                  </div>
                  <div class="text-subtitle1 text-caption">
                    <q-skeleton type="text" />
                  </div>
                  <div>
                    <q-skeleton type="text" />
                  </div>
                </q-card-section>

                <q-card-section class="q-pt-none"> </q-card-section>
              </q-card>
            </div>
          </div>
        </div>
      </template>
      <card-for-data
        :showRoute="showRoute"
        :suggestedRoute="this.suggestedRoute"
        @closeDialog="showRoute = false"
      />
    </q-page>
  </div>
</template>

<script>
import CardForData from "components/CardForData.vue";
import WeatherData from "../components/WeatherBanner.vue";
import { LocalStorage } from "quasar";
export default {
  name: "SearchCityPage",
  components: {
    CardForData,
    WeatherData,
  },
  data() {
    return {
      search: "",
      showRoute: false,
      loadingPlaces: false,
      showBanner: true,
      travelData: null,
      showHotels: true,
      showRes: true,
      showAttraction: true,
      searchInfo: {},
      Preferences: [],
      restaurants: [],
      suggestedRestaurants: [],
      hotels: [],
      selectedHotel: {},
      weatherInfo: [],
      attractions: [],
      travelInfo: [],
      searchData: [],
      suggestedRoute: {},
      images: [],
      lat: 21.1458,
      lon: 79.0882,
      apiUrl: "https://discover.search.hereapi.com/v1/discover",
      apiUrlAuto: "https://autocomplete.search.hereapi.com/v1/autocomplete",
      apiKey: "BPW-o8N1SNyob4s9eQhPlJsOuHEGSgcZOLrEWOh-0Nw",
    };
  },
  computed: {},
  methods: {
    getDataBySearch() {
      this.getData();
      this.searchInfo = {};
      this.$axios(
        `${this.apiUrl}?at=${this.lat},${this.lon}&q=${this.search}&apiKey=${this.apiKey}&units=metric`
      )
        .then((response) => {
          console.log("response.data: ", response.data);
          console.log("search:  ", this.search);

          let searchData = {
            title: response.data.items[0].title,
            resultType: response.data.items[0].resultType,
            address: response.data.items[0].address,
            position: response.data.items[0].position,
          };
          this.searchInfo = searchData;
          console.log("searchInfo: ", this.searchInfo);
          this.lat = this.searchInfo.position.lat;
          this.lon = this.searchInfo.position.lng;
          console.log("lat&lng: ", this.lat, this.lon);
        })
        .catch((err) => {
          console.log("error : ", err);
          // this.dataError();
        });
      console.log("searchInfo: ", this.searchInfo);
    },
    dataError() {
      this.$q.notify({
        message: "Unable to fetch data",
        color: "purple",
      });
      // this.$q.dialog({
      //   title: "Error",
      //   message: "Could not find your location",
      // });
    },
    getPreferences(preference) {
      this.$axios(
        `${this.apiUrl}?at=${this.lat},${this.lon}&q=${preference}&apiKey=${this.apiKey}&units=metric`
      )
        .then((response) => {
          //console.log('response.data: ', response.data);
          response.data.items.forEach((data) => {
            let preferData = {
              title: data.title,
              resultType: data.resultType,
              address: data.address,
              position: data.position,
            };
            if (preference == "restaurants") {
              this.restaurants.push(preferData);
            }
            if (preference == "hotels") {
              this.hotelPrefer.push(preferData);
            }
            if (preference == "attractions") {
              this.attractions.push(preferData);
            }
          });
        })
        .catch((err) => {
          console.log("error : ", err);
          this.dataError();
        });
    },
    showingData(show) {
      this.showHotels = false;
      this.showRes = false;
      this.showAttraction = false;
      if (show == "hotels") {
        this.showHotels = true;
      }
      if (show == "restaurants") {
        this.showRes = true;
      }
      if (show == "attractions") {
        this.showAttraction = true;
      }
    },
    getData() {
      var options = {
        method: "GET",
        url: "https://travel-advisor.p.rapidapi.com/locations/search",
        params: {
          query: this.search,
          limit: "30",
          offset: "0",
          units: "km",
          location_id: "1",
          currency: "USD",
          sort: "relevance",
          lang: "en_US",
        },
        headers: {
          "x-rapidapi-host": "travel-advisor.p.rapidapi.com",
          "x-rapidapi-key":
            "14a01a08bcmsh3c7a158bfb04e56p1ee3b0jsn4edd7a64166a",
        },
      };
      this.loadingPlaces = true;
      this.$axios
        .request(options)
        .then((response) => {
          this.searchData = [];
          this.searchData = response.data.data;
          this.Preferences = this.searchData;

          this.searchData.forEach((data) => {
            this.images.push(data.result_object.photo);
          });
          this.loadingPlaces = false;
          console.log("getData: ", this.searchData);
          this.getBestHotel();
        })
        .catch(function (error) {
          console.log("Error: ", error);
          this.loadingPlaces = false;
        });
    },
    getHotels() {
      this.searchData = this.Preferences;
      this.searchData = this.searchData.filter(
        (hotel) =>
          hotel.result_type === "lodging" || hotel.result_type === "geos"
      );
      console.log("gethOTELS: ", this.searchData);
    },
    getAttractions() {
      this.searchData = this.Preferences;
      this.searchData = this.searchData.filter(
        (attract) =>
          attract.result_type === "things_to_do" ||
          attract.result_type === "geos"
      );
      console.log("getATTRAC: ", this.searchData);
    },
    getRestaurants() {
      this.searchData = this.Preferences;
      this.searchData = this.searchData.filter(
        (hotel) =>
          hotel.result_type === "restaurants" || hotel.result_type === "geos"
      );
      console.log("getRest: ", this.searchData);
    },
    getWeather() {
      var options = {
        method: "GET",
        url: "https://weatherapi-com.p.rapidapi.com/forecast.json",
        params: { q: this.search, days: "8" },
        headers: {
          "x-rapidapi-host": "weatherapi-com.p.rapidapi.com",
          "x-rapidapi-key":
            "e73407ea73msh8465bcf016aa369p15de86jsn982d6e844738",
        },
      };

      this.$axios
        .request(options)
        .then((response) => {
          this.weatherInfo = [];
          response.data.forecast.forecastday.forEach((data) => {
            let weather = {
              date: data.date,
              day: data.day,
              hour: data.hour,
            };
            console.log("forEach data: ", weather);
            this.weatherInfo.push(weather);
          });
          console.log("weatherData: ", this.weatherInfo);
        })
        .catch(function (error) {
          console.error(error);
        });
    },
    getDistanceFromLatLonInKm(lat1, lon1, lat2, lon2) {
      var R = 6371; // Radius of the earth in km
      var dLat = this.deg2rad(lat2 - lat1); // deg2rad below
      var dLon = this.deg2rad(lon2 - lon1);
      var a =
        Math.sin(dLat / 2) * Math.sin(dLat / 2) +
        Math.cos(this.deg2rad(lat1)) *
          Math.cos(this.deg2rad(lat2)) *
          Math.sin(dLon / 2) *
          Math.sin(dLon / 2);
      var c = 2 * Math.atan2(Math.sqrt(a), Math.sqrt(1 - a));
      var d = R * c; // Distance in km
      return d;
    },

    deg2rad(deg) {
      return deg * (Math.PI / 180);
    },

    getBestHotel() {
      var suggestedAttraction = [];
      this.suggestedRestaurants = [];
      var suggestedHotels = [];
      this.attractions = [];
      this.restaurants = this.searchData.filter(
        (hotel) => hotel.result_type === "restaurants"
      );
      // console.log('restaurants: ', this.restaurants);
      this.hotels = this.searchData.filter(
        (hotel) => hotel.result_type === "lodging"
      );
      // console.log('hotels: ', this.hotels);
      this.attractions = this.searchData.filter(
        (attr) =>
          attr.result_type === "things_to_do" ||
          attr.result_type === "activities"
      );
      
      var pointOfRef = this.searchData[0]
      console.log('por: ', pointOfRef);

      //this.attractions = this.attractions.sort(function(a, b){return a.rating-b.rating})
      // console.log('attractions: ', this.attractions)
      this.hotels.forEach((hotel) => {
        // console.log('localeCompare: ', hotel.result_object.rating.localeCompare("4.5"));
        suggestedHotels.push({
          distance: this.getDistanceFromLatLonInKm(
            pointOfRef.result_object.latitude,
            pointOfRef.result_object.longitude,
            hotel.result_object.latitude,
            hotel.result_object.longitude
          ),
          hotels: hotel,
        })
      });

      suggestedHotels =  suggestedHotels.sort(function (a, b) {
        return a.distance - b.distance;
      });
      console.log('suggestedHotels: ', suggestedHotels);
      this.selectedHotel = suggestedHotels[0].hotels

      console.log('selectedHotel: ', this.selectedHotel);
      

      // console.log('selectedHotel: ', this.selectedHotel);
      this.restaurants.forEach((restaurant) => {
        this.suggestedRestaurants.push({
          distance: this.getDistanceFromLatLonInKm(
            this.selectedHotel.result_object.latitude,
            this.selectedHotel.result_object.longitude,
            restaurant.result_object.latitude,
            restaurant.result_object.longitude
          ),
          restaurant: restaurant,
        });
      });
      this.suggestedRestaurants = this.suggestedRestaurants.sort(function (
        a,
        b
      ) {
        return a.distance - b.distance;
      });
      this.attractions.forEach((attraction) => {
        suggestedAttraction.push({
          distance: this.getDistanceFromLatLonInKm(
            this.suggestedRestaurants[0].restaurant.result_object.latitude,
            this.suggestedRestaurants[0].restaurant.result_object.longitude,
            attraction.result_object.latitude,
            attraction.result_object.longitude
          ),
          attraction: attraction,
        });
      });
      suggestedAttraction = suggestedAttraction.sort(function (a, b) {
        return a.distance - b.distance;
      });
      // console.log('suggestedAttraction: ', suggestedAttraction);
      // console.log('suggestedRestaurants: ', this.suggestedRestaurants.sort(function(a, b){return a.distance-b.distance}));
      this.suggestedRoute = {
        hotel: this.selectedHotel,
        restaurant: this.suggestedRestaurants[0],
        time_restaurant: this.suggestedRestaurants[0].distance / 40,
        attraction: suggestedAttraction[0],
        time_attraction: suggestedAttraction[0].distance / 40,
      };
      console.log("suggestedRoute: ", this.suggestedRoute);
    },
  },
  created() {
    this.search = LocalStorage.getItem("searchName");
    this.getDataBySearch();
    this.getWeather();
    this.getData();
  },
  destroyed() {
    // LocalStorage.set("searchName", '')
  },
};
</script>

<style>
.mycard {
  width: 100%;
  height: 400px;
  max-width: 280px;
  border-radius: 10px;
}
</style>