<!-- Begin Traffic Light Component -->
<template>
  <div class="trafficLightDiv">
    <!-- 
      Below Div toggles the class between amberInProgress and notInProgress 
      to show traffic light in amber when a call is being made to the server
      indicating the server is busy and is checking status every 10 seconds
    -->
    <div 
    :class="localInProgress ? 'amberInProgress' : 'notInProgress'"
    >
    <!-- If 'success' is true, red light is dimmed by red-200 class and green light is illuminated by green-200 class CSS -->
    <!-- If 'success' is false, red light is dimmed by red-200 class and green light is illuminated by green-200 class CSS -->

    <div class="redGreenDiv">
        <div
          :class="localSuccess ? 'red-200' : 'red-not-200'"
        ></div>
        <div
          :class="localSuccess ? 'green-200' : 'green-not-200'"
        ></div>
      </div>
    </div>
    <!-- View Server Details shows details about this traffic light like Server name, description, URL and server's current status -->
    <p class="urlChecked">
      {{ servername }}<br>
      <span class="viewServerDetailsLink" @click="viewServerDetails">View Server Details</span>
    </p>
    <!-- Manually triggers server status check -->
    <input
      type="button"
      class="checkStatusButton trafficLightButton"
      @click="checkStatus"
      value="Check Status"
    />
    <!-- Removes this Traffic Light Component -->
    <input
      type="button"
      class="removeTrafficLightButton trafficLightButton"
      @click="removeLight"
      value="Remove Light"
    />
  </div>
</template>

<script>
export default {
  name: "TrafficLight",
  props: {
    url: String,
    success: Boolean,
    inProgress: Boolean,
    servername: String,
    serverdescription: String
  },
  data: function () {
    return {
      intervalId: null,
      localSuccess: this.success,
      localInProgress: this.inProgress,
    };
  },
  methods: {
    removeLight() {
      this.$emit("remove-light");
    },
    viewServerDetails() {
      var viewServerStatusValue = this.localSuccess ? "Successfully Running" : "Failure"
      //This method of displaying server details is done as a placeholder - Could be replaced with Fancybox or Lightbox or other modal plugins in real applications
      alert(`Server Name: ${this.servername}\nServer Description: ${this.serverdescription}\nServer URL: ${this.url}\nServer Current Status: ${viewServerStatusValue}`);
    },
    checkStatus() {
      const requestOptions = {
        method: 'GET',
        headers: {'Content-Type': 'application/json'},
      }
      // Parsing the URL as a string as it is read from a text field and reading status code parameter
      var code = this.url;
      code = code.substring(code.indexOf('?') + 1);
      code = code.split('&')[0].split('=')[1];
      /* 
        Fetch calls to the NodeJS backend to the status endpoint with the 
        status code and without any delay as check status needs to be immediate 
      */
      fetch(`http://localhost:4000/status?code=${code}`,requestOptions)
      .then((response) => {
      this.localInProgress = true; // The localInProgress when set to true makes the traffic light's color to amber
      if (response.status === 200) {
        this.localSuccess = true;
      } else {
        this.localSuccess = false;
      }
      console.log(`Status for traffic light currently added is ${response.status}`)  
      setTimeout(() => {  this.localInProgress = false; }, 400); // This removes amber light from the traffic light after a 0.4 second delay
      })
      .catch((err) => {
        console.log(err)
      })
    }
  },


  created() {
    const delay = 10 * 1000; //Delay of 10 seconds as the interval for the traffic light to repeat server check

    const requestOptions = {
        method: 'GET',
        headers: {'Content-Type': 'application/json'},
    }
    /* 
      Fetchdata calls to NodeJS backend with the traffic light component's URL and updates localSuccess value
      based on the status returned. It also sets the light color to amber when in progress
    */
    const fetchData = () => {
      fetch(`http://localhost:4000${this.url}`,requestOptions)
      .then((response) => {
        this.localInProgress = true;
        if (response.status === 200) {
          this.localSuccess = true;
        } else {
          this.localSuccess = false;
        }
        console.log(`Status for traffic light currently added is ${response.status}`)  
        setTimeout(() => {  this.localInProgress = false; }, 400);
      })
      .catch((err) => {
        console.log(err)
      })
    };
    fetchData();
    this.intervalId = setInterval(fetchData, delay); //This sets fetchData to be called in loop with Set Interval of 10 seconds
  },
  destroyed() {
    clearInterval(this.intervalId); // To close the infinite loop when component is destroyed
  },
};
</script>

<style scoped>
.trafficLightInnerContainer .trafficLightDiv {
  display: inline-block;
  width: 160px;
  margin: 10px;
  vertical-align: top;
  padding: 10px;
  border: 1px solid #000000;
}
.redGreenDiv {
  width: 150px;
  height: 260px;
  border: 5px solid black;
  background: black;
}
.amberInProgress .redGreenDiv {
  border: 5px solid #FFBF00!important;
}
.amberInProgress .redGreenDiv .red-not-200 {
  background: #FFBF00;
  -webkit-animation: none!important; /* Safari 4+ */
  -moz-animation: none!important; /* Fx 5+ */
  -o-animation: none!important; /* Opera 12+ */
  animation: none!important; /* IE 10+, Fx 29+ */
}
.amberInProgress .redGreenDiv .green-200 {
  background: #FFBF00;
}
.red-200 {
  position: relative;
  margin: 0 auto;
  width: 100px;
  height: 100px;
  background: #ff0000;
  border-radius: 50%;
  top: 5%;
  opacity: 0.2;
}

.green-200 {
  position: relative;
  margin: 0 auto;
  width: 100px;
  height: 100px;
  background: #00ff00;
  border-radius: 50%;
  top: 15%;
  opacity: 1;
}
.red-not-200 {
  position: relative;
  margin: 0 auto;
  width: 100px;
  height: 100px;
  background: #ff0000;
  border-radius: 50%;
  top: 5%;
  opacity: 1;
  -webkit-animation: flash 1s infinite; /* Safari 4+ */
  -moz-animation: flash 1s infinite; /* Fx 5+ */
  -o-animation: flash 1s infinite; /* Opera 12+ */
  animation: flash 1s infinite; /* IE 10+, Fx 29+ */
}
.green-not-200 {
  position: relative;
  margin: 0 auto;
  width: 100px;
  height: 100px;
  background: #00ff00;
  border-radius: 50%;
  top: 15%;
  opacity: 0.4;
}
.urlChecked {
  font-family: "Heebo",Helvetica,Arial,sans-serif !important;
  font-weight: bold;
  text-align: center;
  word-wrap: break-word;
  height: 50px;
  line-height: 22px;
}
.urlChecked .viewServerDetailsLink {
  cursor: pointer;
  text-decoration: underline;
}
.trafficLightButton {
  display: block;
  width: 120px;
  font-family: "Heebo",Helvetica,Arial,sans-serif !important;
  color: #fff;
  background-color: #1B77C9;
  margin: 10px auto;
  padding: 5px 15px;
  border-color: #1B77C9;
  border-radius: 5px;
  cursor: pointer;
}
.trafficLightButton:hover {
  background-color: #a0a0a0;
  border-color: #a0a0a0;
}

/* Animation to control traffic light's flashing red light */

/* Safari 4+ */
@-webkit-keyframes flash {
  0% {
    opacity: 1;
  }
  100% {
    opacity: 0.1;
  }
}
/* Fx 5+ */
@-moz-keyframes flash {
  0% {
    opacity: 1;
  }
  100% {
    opacity: 0.1;
  }
}
/* Opera 12+ */
@-o-keyframes flash {
  0% {
    opacity: 1;
  }
  100% {
    opacity: 0.1;
  }
}
/* IE 10+, Fx 29+ */
@keyframes flash {
  0% {
    opacity: 1;
  }
  100% {
    opacity: 0.1;
  }
}
</style>
