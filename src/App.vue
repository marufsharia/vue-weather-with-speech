<template>
  <div id="app" :class="typeof weather.main !='undefined' && weather.main.temp >16 ? 'warm':''">
    <link
      rel="stylesheet"
      href="https://use.fontawesome.com/releases/v5.2.0/css/all.css"
      integrity="sha384-hWVjflwFxL6sNzntih27bfxkr27PmbbK/iSvJ+a4+0owXq79v+lsFkW54bOGbiDQ"
      crossorigin="anonymous"
    />
    <main>
      <div class="search-box">
        <input
          type="text"
          class="search-bar"
          placeholder="Search..."
          v-model="query"
          @keypress="fetchWeather"
        />
      </div>

      <div class="weather-wrap" v-if="typeof weather.main !='undefined'">
        <div class="location-box">
          <div class="location">{{weather.name}}, {{weather.sys.country}}</div>
          <div class="date">{{dateBuilder()}}</div>
        </div>

        <div class="weather-box">
          <div class="temp">{{Math.round(weather.main.temp)}}&deg;c</div>
          <div class="weather">{{weather.weather[0].main}}</div>
          <div class="description">
            <i class="fa fa-tag"></i>
            {{weather.weather[0].description}}
          </div>
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
      api_key: "082f7cdad50130912c961bf92b7b15dc",
      url_base: "https://api.openweathermap.org/data/2.5/",
      query: "",
      weather: {}
    };
  },

  methods: {
    fetchWeather(e) {
      if (e.key == "Enter") {
        fetch(
          `${this.url_base}weather?q=${this.query}&units=metric&APPID=${this.api_key}`
        )
          .then(res => {
            return res.json();
          })
          .then(this.setResult);
           
      }
    },
    setResult(results) {
      this.weather = results;
      this.textToSpeech();
    },
    log(msg) {
      ~~// eslint-disable-next-line no-console
      console.log(msg);
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
        "December"
      ];
      let days = [
        "Sunday",
        "Monday",
        "Tuesday",
        "Wednesday",
        "Thursday",
        "Friday",
        "Saturday"
      ];

      let day = days[d.getDay()];
      let date = d.getDate();
      let month = months[d.getMonth()];
      let year = d.getFullYear();

      return `${day} ${date} ${month} ${year}`;
    },

    //for text to speech
    textToSpeech() {
      // get all voices that browser offers
      var available_voices = window.speechSynthesis.getVoices();
      this.log(available_voices);

      // this will hold an english voice
      var english_voice = "";

      // find voice by language locale "en-US"
      // if not then select the first voice
      for (var i = 0; i < available_voices.length; i++) {
        if (available_voices[i].lang === "en-US") {
          english_voice = available_voices[i];
          if (
            english_voice.voiceURI ==
            "Microsoft Zira Desktop - English (United States)"
          ) {
            break;
          }
        }
      }
      this.log(english_voice);
      if (english_voice === "") english_voice = available_voices[0];

      // new SpeechSynthesisUtterance object
      var utter = new SpeechSynthesisUtterance();
      utter.rate = 0.7;
      utter.pitch = 0.5;
      utter.text = `Today is ${this.dateBuilder()}. ${
        this.weather.name
      }'s weather is ${Math.round(this.weather.main.temp)} degree celcius.`;
      utter.voice = english_voice;

      // // event after text has been spoken
      // utter.onend = function() {
      // 	alert('Speech has finished');
      // }

      // speak
      window.speechSynthesis.speak(utter);
    }
  }
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
  background-image: url("./assets/cold-bg.jpg");
  background-size: cover;
  background-position: bottom;
  transition: 0.4s;
}

#app.warm {
  background-image: url("./assets/warm-bg.jpg");
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

.search-box {
  width: 100%;
  margin-bottom: 30px;
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
  font-weight: 300;
  text-align: center;
  text-shadow: 1px 3px rgba(0, 0, 0, 0.25);
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
  border-radius: 16px;
  margin: 30px 0px;
  box-shadow: 3px 6px rgba(0, 0, 0, 0.25);
}
.weather-box .weather {
  color: #fff;
  font-size: 48px;
  font-weight: 700;
  font-style: italic;
  text-shadow: 3px 6px rgba(0, 0, 0, 0.25);
}

.weather-box .description {
  margin-top: 5px;
  color: #ededed;
  font-size: 18px;
  font-weight: 300;
  text-shadow: 3px 6px rgba(0, 0, 0, 0.25);
}
</style>
