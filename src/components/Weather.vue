<script setup>

</script>


<script>
export default {
  data() {
    return {
      data: {},
      location: 'Location',
      selectedDay: 'today',
      icon: "50d",
      tempData: [
        { time: '1 AM', icon: '01d', temperature: 25 },
        { time: '2 AM', icon: '02d', temperature: 25 },
        { time: '3 AM', icon: '03d', temperature: 23 },
        { time: '4 AM', icon: '04d', temperature: 22 },
        { time: '5 AM', icon: '01d', temperature: 20 },
        { time: '6 AM', icon: '02d', temperature: 25 },
        { time: '7 AM', icon: '03d', temperature: 25 },
        { time: '8 AM', icon: '04d', temperature: 23 },
        { time: '9 AM', icon: '01d', temperature: 22 },
        { time: '10 AM', icon: '02d', temperature: 22 },
      ],

    };
  },
  computed: {

    groupedTempData() {
      const groups = [{ time: '1 AM to 5 AM', hours: [] }, { time: '6 AM to 10 AM', hours: [] }];
      let currentGroup = null;

      this.tempData.forEach((hour) => {
        if (hour.time.includes('AM')) {
          // Determine the group based on the hour
          if (parseInt(hour.time) <= 5) {
            currentGroup = groups[0];
          } else {
            currentGroup = groups[1];
          }
        }
        currentGroup.hours.push(hour);
      });

      return groups;
    },
  },
  mounted() {
    const connectedStatus = window.navigator.onLine ? 'online' : 'offline';
    console.log(`connected ${connectedStatus}`);
    Toastify({
      text: `Connected ${connectedStatus}`,
      duration: 3000,
      gravity: 'top',
      position: "center",
      backgroundColor: 'white',


    }).showToast();
    const toast = document.querySelector(".toastify")
    if (toast) {
      toast.style.color = 'blue'
    }
    this.fetchData()
    this.getLocation();
  },
  methods: {
    async fetchData(latitude, longitude) {
      console.log("fetching data");
      try {
        const response = await fetch(`https://api.openweathermap.org/data/2.5/weather?lat=${latitude}&lon=${longitude}&appid=f98e2f746a585c1ceb1560d83895c584`);
        console.log(response);
        console.log(latitude, longitude);
        const data = await response.json();
        this.data = data;
        console.log(data, 'data');
      } catch (error) {
        console.error(error);
      }
    },
    getLocation() {
      if (navigator.geolocation) {
        navigator.geolocation.getCurrentPosition(
          (position) => {
            const latitude = position.coords.latitude;
            const longitude = position.coords.longitude;
            this.location = `Latitude: ${latitude}, Longitude: ${longitude}`;
            this.fetchData(latitude, longitude);
          },
          (error) => {
            this.location = `Error while getting location: ${error.message}`;
          }
        );
      } else {
        this.location = 'Geolocation cannot be accessed by this browser.';
      }
    },
    getWeatherIconUrl(iconCode) {
      return `https://openweathermap.org/img/wn/${iconCode}.png`;
    },
    getCurrentDate() {
      const currentDate = new Date();
      const options = { year: 'numeric', month: 'long', day: 'numeric' };
      return currentDate.toLocaleDateString('en-US', options);
    },
  },
};
</script>





<template>
  <div class="first p-4 flex " >
    <main class="main text-center relative z-10 bg-transparent ml-0">
      <!-- Weather information -->
      <div class="third mb-4 p-8 rounded-md">
        <!-- Dropdown for Today, Tomorrow, Yesterday -->
        <div class="mb-4 flex justify-center">
          <select v-model="selectedDay" class="p-2 rounded-md bg-transparent">
            <option value="today">Today</option>
            <option value="tomorrow">Tomorrow</option>
            <option value="yesterday">Yesterday</option>
          </select>
        </div>

        <!-- Icon and temperature -->
        <div class="location-temperature">
          <div class="flex items-center" style="margin-left: -1.5rem">
            <img v-if="data.weather && data.weather.length > 0"
              :src="'https://openweathermap.org/img/wn/' + data.weather[0].icon + '.png'" alt="Weather Icon"
              class="weather-icon mt-0 mx-3">
            <p v-if="data.main && typeof data.main.temp !== undefined" class="temperature text-4xl font-bold">{{
              data.main.temp || 'N/A' }}°</p>
          </div>
        </div>


       <!--Weather description -->
        <div class="weather-description text-lg mb-2 mt-1">
          {{ data.weather?.[0]?.description }}

        </div>

        <!-- Location -->
        <div class="location text-lg mb-2">{{ data.name }}</div>



        <!-- Additional Weather Information -->
        <div class="additional-info text-sm">
          <div class="mb-auto mt-auto">{{ getCurrentDate() }}</div>
          <div class="mb-auto mt-3" v-if="data.main && data.main.feels_like !== undefined">
            Feels Like: {{ data.main.feels_like }} | Sunset: 18:20
          </div>
        </div>


      </div>

    </main>

    <!-- Temperatures with icon and time -->
    <div class="time-info mt-0 relative " style="margin-left: 10rem; color: white;">
      <div style="border-radius: 10px;">
        <div v-for="(group, index) in groupedTempData" :key="index" class="temp-group flex gap-5 rounded">
          <div v-for="(hour, hourIndex) in group.hours" :key="hourIndex" class="temp-item">
            <div class="flex items-center">
              <p>{{ hour.time }}</p>
            </div>
            <div class="flex items-center" style="margin-left: 0rem;">
              <img :src="getWeatherIconUrl(hour.icon)" alt="Weather Icon" class="temp-icon" style="margin-left: -10px;">
              <p style="margin-left: -5px;">{{ hour.temperature }}°</p>
            </div>
            <!-- Show divider after 5 AM and 10 AM  -->
            <template v-if="['1 AM', '2 AM', '3 AM', '4 AM', '5 AM'].includes(hour.time)">
              <hr class="divider my-2">
            </template>
          </div>
        </div>
      </div>

      <div class="randomText mt-16">
        <div class="title">Random Text</div>
        <p class="para">Improve him believe opinion offered met and end cheered forbade. Friendly as stronger speedily by
          recurred. Son interest wandered sir addition end say. Manners beloved affixed picture men ask.</p>
      </div>
    </div>

  </div>
</template>











<style scoped>
.first {
  font-family: 'Arial', sans-serif;
  Height: 421px;
  Top: -601px;
  border-radius: 35px;
}

.location {
  font-size: 1rem;
}

.temperature {
  font-size: 4rem;
  font-weight: 500;
  /* width: auto; */
}

.weather-description {
  font-size: 1.25rem;
  font-weight: bold;
}

.third {
  color: #e38262
}

.weather-icon {
  width: 100px;
  height: 100px;
}

.main {
  background: #FAE2BD;
  width: 360px;
  height: 380px;
  /* top: 64px; */
  left: -90px;
  border-radius: 35px;
  margin-left: 10rem;
}



.first::before {
  content: '';
  position: absolute;
  inset: 0;
  background-image: url('https://getwallpapers.com/wallpaper/full/7/b/1/808242-pink-background-images-1920x1080-ios.jpg');
  background-size: cover;
  filter: blur(8px);
}


.temp-group {
  color: black;
  padding: 0rem 1rem 1rem 4rem;
  Top: 66px;
  Left: 430px;
  background: radial-gradient(97.57% 210.75% at 0.9% 2.98%,
      rgba(250, 226, 189, 0.525) 0%,
      rgba(250, 226, 189, 0.225) 100%);
}

.temp-item {
  text-align: center;
}

.temp-icon {
  width: 30px;
  height: 30px;
  margin: 5px;
}

.divider {
  border-top: 1px solid #ccc;
}

.randomText {
  color: black;
  width: 444px;
  height: 92px;
  top: 329px;
  left: 441px;
  opacity: 0.75px
}

.para {
  font-size: 15px;
  font-weight: 500;
  line-height: 23px;
  letter-spacing: 0em;
  text-align: left;
  margin-top: 1rem;

}

.title {
  font-family: Arial, Helvetica, sans-serif;
  font-size: 20px;
  font-weight: 600;
  line-height: 30px;
  letter-spacing: 0em;
  text-align: left;

}
@media (max-width: 768px) {
  .first {
    flex-direction: column;
  }
  

  .main {
    width: auto;
    height: auto;
    left: 0;
    margin-top: -10rem;
    margin-left: 4rem;
    margin-bottom: 4rem;
  }

  .temp-group {
    margin-left: -8rem;
    padding-left: 3rem;
    padding-top: 1rem;
  }

  .time-info{
 
    color: black;

  } 

  .randomText {
    width: 30rem;
    height: auto;
    margin-top: 4rem;
    margin-left: -8rem;
    align-items: center;
    text-align: center;
    opacity: 1;
  }
}
</style>
