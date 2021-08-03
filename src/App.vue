<template>
  <!-- google fonts include -->
  <link rel="preconnect" href="https://fonts.googleapis.com" />
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin />
  <link
    href="https://fonts.googleapis.com/css2?family=Bebas+Neue&display=swap"
    rel="stylesheet"
  />

  <!-- bootstrap include for icon-->
  <link
    rel="stylesheet"
    href="https://cdn.jsdelivr.net/npm/bootstrap-icons@1.5.0/font/bootstrap-icons.css"
  />

  <div
    id="app"
    :class="typeof weather.main != 'undefined'"
    :style="{
      backgroundImage: 'url(' + image + ')',
    }"
  >
    <main>
      <div class="header-wrap">
        <div class="header">cloud.world <i class="bi bi-cloud-haze"></i></div>
        <!-- On click make it go to my personal website -->
      </div>
      <div class="search-box">
        <input
          type="text"
          class="search-bar"
          placeholder="Search..."
          v-model="query"
          @keyup.enter="fetchWeather"
          @keydown.enter="bgImg"
        />
      </div>

      <div class="weather-wrap" v-if="typeof weather.main != 'undefined'">
        <div class="location-box">
          <div class="location">
            {{ weather.name }}, {{ weather.sys.country }}
          </div>
          <div class="date">{{ dateBuilder() }}</div>
        </div>

        <div class="weather-box">
          <div class="temp" v-on:click="swapMetric">
            {{ Math.round(weather.main.temp) }} {{ metric }}
          </div>

          <div class="weather">{{ weather.weather[0].main }}</div>
        </div>
      </div>
    </main>
  </div>
</template>

<script>
export default {
  name: "app",
  data() {
    return {
      //Weather
      api_key: process.env.VUE_APP_weatherAPI,
      url_base: process.env.VUE_APP_weatherURL,
      //Background Images
      pexels_api: process.env.VUE_APP_pexelsAPI,
      pexels_base: process.env.VUE_APP_pexelsURL,

      //Data
      query: "",
      weather: {},
      image: "",
      swap: 0,
      metric: "C°",
    };
  },
  methods: {
    swapMetric() {
      if (this.metric === "C°") {
        let swap = this.weather.main.temp;
        swap = swap * (9 / 5);
        swap = swap + 32;
        this.swap = swap;
        this.weather.main.temp = swap;
        this.metric = "F°";
      } else if (this.metric === "F°") {
        this.fetchWeather();
        this.metric = "C°";
      }
    },
    bgImg() {
      fetch(`https://api.pexels.com/v1/search?query=${this.query}`, {
        headers: {
          Authorization: this.pexels_api,
        },
      })
        .then((resp) => {
          return resp.json();
        })
        .then((data) => {
          this.image = data.photos[0].src.large;
        });
    },
    fetchWeather() {
      fetch(
        `${this.url_base}weather?q=${this.query}&units=metric&APPID=${this.api_key}`
      )
        .then((res) => {
          return res.json();
        })
        .then(this.setResults);
    },
    setResults(results) {
      this.weather = results;
      this.metric = "C°";
    },
    dateBuilder() {
      let d = new Date();
      let months = [
        "January",
        "February",
        "March",
        "April",
        "May",
        "June",
        "July",
        "August",
        "September",
        "October",
        "November",
        "December",
      ];
      let days = [
        "Sunday",
        "Monday",
        "Tuesday",
        "Wednesday",
        "Thursday",
        "Friday",
        "Saturday",
      ];
      let day = days[d.getDay()];
      let date = d.getDate();
      let month = months[d.getMonth()];
      let year = d.getFullYear();
      return `${day}, ${date} ${month} ${year}`;
    },
  },
};
</script>

<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}
body {
  font-family: "montserrat", sans-serif;
}
#app {
  background-image: url("https://images.unsplash.com/photo-1475319122043-5ca9eeceefaf?ixid=MnwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8&ixlib=rb-1.2.1&auto=format&fit=crop&w=1500&q=80");
  transition: 1s;
  background-size: cover;
  background-position: bottom;
  transition: 0.4s;
}

main {
  min-height: 100vh;
  padding: 25px;
  background-image: linear-gradient(
    to bottom,
    rgba(0, 0, 0, 0.25),
    rgba(0, 0, 0, 0.75)
  );
}
.header-wrap {
  display: flex;
}
.header {
  margin-left: auto;
  font-size: 25px;
  font-weight: 300;
  color: #fff;
  margin-bottom: 25px;
  border-radius: 0px 16px;
  padding: 10px;
  font-family: "Bebas Neue", sans-serif;
  text-shadow: 2px 3px rgba(0, 0, 0, 1);
}

.search-box {
  width: 100%;
  max-width: 700px;
  margin-bottom: 30px;
  margin-left: auto;
  margin-right: auto;
}
.search-box .search-bar {
  display: block;
  width: 100%;
  padding: 15px;

  color: #313131;
  font-size: 20px;
  appearance: none;
  border: none;
  outline: none;
  background: none;
  box-shadow: 0px 0px 8px rgba(0, 0, 0, 0.25);
  background-color: rgba(255, 255, 255, 0.5);
  border-radius: 0px 16px 0px 16px;
  transition: 0.4s;
}
.search-box .search-bar:focus {
  box-shadow: 0px 0px 16px rgba(0, 0, 0, 0.25);
  background-color: rgba(255, 255, 255, 0.75);
  border-radius: 16px 0px 16px 0px;
}
.location-box .location {
  color: #fff;
  font-size: 32px;
  font-weight: 500;
  text-align: center;
  text-shadow: 1px 3px rgba(0, 0, 0, 0.25);
  transition: 0.4s;
  margin-bottom: 2px;
}
.location-box .date {
  color: #fff;
  font-size: 20px;
  font-weight: 300;
  font-style: italic;
  text-align: center;
}

.weather-box {
  text-align: center;
}
.weather-box .temp {
  display: inline-block;
  padding: 10px 25px;
  color: #fff;
  font-size: 102px;
  font-weight: 900;
  text-shadow: 3px 6px rgba(0, 0, 0, 0.25);
  background-color: rgba(255, 255, 255, 0.2);
  border-radius: 0px 16px 0px 16px;
  margin: 30px 0px;
  box-shadow: 3px 6px rgba(0, 0, 0, 0.25);
  transition: 0.4s;
}
.weather-box .weather {
  color: #fff;
  font-size: 48px;
  font-weight: 700;
  font-style: italic;
  text-shadow: 3px 6px rgba(0, 0, 0, 0.25);
}

.weather-box .temp:hover {
  background-color: rgba(0, 0, 0, 0.25);
  cursor: pointer;
}
</style>
