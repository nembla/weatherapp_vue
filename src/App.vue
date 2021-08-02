<template>
  <!-- google fonts include -->
  <link rel="preconnect" href="https://fonts.googleapis.com" />
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin />
  <link
    href="https://fonts.googleapis.com/css2?family=Bebas+Neue&display=swap"
    rel="stylesheet"
  />

  <!-- bootstrap include for icon -->
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
      api_key: "3e1b13963b70c89ff5f1d2bb337e9121",
      url_base: "https://api.openweathermap.org/data/2.5/",
      pexels_api: "563492ad6f9170000100000100847098ed934225a39aeab5b90bf25c",
      pexels_base: "https://api.pexels.com/v1/",
      query: "",
      weather: {},
      image: "",
      onClick: false,
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
      return `${day} ${date} ${month} ${year}`;
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
  background-color: rgba(255, 255, 255, 0.25);
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

.switch {
  position: relative;
  display: block;
  width: 100px;
  height: 30px;
  padding: 3px;
  margin-left: auto;
  margin-right: auto;
  background: linear-gradient(to bottom, #eeeeee, #ffffff 25px);
  background-image: -webkit-linear-gradient(top, #eeeeee, #ffffff 25px);
  border-radius: 18px;
  box-shadow: inset 0 -1px white, inset 0 1px 1px rgba(0, 0, 0, 0.05);
  cursor: pointer;
  box-sizing: content-box;
}
.switch-input {
  position: absolute;
  top: 0;
  left: 0;
  opacity: 0;
  box-sizing: content-box;
}
.switch-label {
  position: relative;
  display: block;
  height: inherit;
  font-size: 10px;
  text-transform: uppercase;
  background: #eceeef;
  border-radius: inherit;
  box-shadow: inset 0 1px 2px rgba(0, 0, 0, 0.12),
    inset 0 0 2px rgba(0, 0, 0, 0.15);
  box-sizing: content-box;
}
.switch-label:before,
.switch-label:after {
  position: absolute;
  top: 50%;
  margin-top: -0.5em;
  line-height: 1;
  -webkit-transition: inherit;
  -moz-transition: inherit;
  -o-transition: inherit;
  transition: inherit;
  box-sizing: content-box;
}
.switch-label:before {
  content: attr(data-off);
  right: 11px;
  color: #aaaaaa;
  text-shadow: 0 1px rgba(255, 255, 255, 0.5);
}
.switch-label:after {
  content: attr(data-on);
  left: 11px;
  color: #ffffff;
  text-shadow: 0 1px rgba(0, 0, 0, 0.2);
  opacity: 0;
}
.switch-input:checked ~ .switch-label {
  background: #2d6a4f;
  box-shadow: inset 0 1px 2px rgba(0, 0, 0, 0.15),
    inset 0 0 3px rgba(0, 0, 0, 0.2);
}
.switch-input:checked ~ .switch-label:before {
  opacity: 0;
}
.switch-input:checked ~ .switch-label:after {
  opacity: 1;
}
.switch-handle {
  position: absolute;
  top: 4px;
  left: 4px;
  width: 28px;
  height: 28px;
  background: linear-gradient(to bottom, #ffffff 40%, #f0f0f0);
  background-image: -webkit-linear-gradient(top, #ffffff 40%, #f0f0f0);
  border-radius: 100%;
  box-shadow: 1px 1px 5px rgba(0, 0, 0, 0.2);
}
.switch-handle:before {
  content: "";
  position: absolute;
  top: 50%;
  left: 50%;
  margin: -6px 0 0 -6px;
  width: 12px;
  height: 12px;
  background: linear-gradient(to bottom, #eeeeee, #ffffff);
  background-image: -webkit-linear-gradient(top, #eeeeee, #ffffff);
  border-radius: 6px;
  box-shadow: inset 0 1px rgba(0, 0, 0, 0.02);
}
.switch-input:checked ~ .switch-handle {
  left: 74px;
  box-shadow: -1px 1px 5px rgba(0, 0, 0, 0.2);
}

/* Transition
========================== */
.switch-label,
.switch-handle {
  transition: All 0.3s ease;
  -webkit-transition: All 0.3s ease;
  -moz-transition: All 0.3s ease;
  -o-transition: All 0.3s ease;
}
</style>
