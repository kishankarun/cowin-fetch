<template>
  <div class="home">
    <div class="field is-grouped">
      <p class="control">
        <label class="label">
          Search by:
          <div class="select is-small">
            <select name="option" id="" v-model="optionType" @change="changeOption">
              <option value="">-- Select PIN or State --</option>
              <option value="pin">PIN</option>
              <option value="state">State</option>
            </select>
          </div>
        </label>
      </p>
    </div>

    <div class="field is-grouped" v-if="false">
      <p class="control">
        <label class="label">
          From date:
          <input type="date" id="date" name="date" v-model="date" @change="formatDate">
        </label>
      </p>
    </div>


    <div class="columns" v-show="!errorVal && optionType === 'state'">
      <div class="column is-one-quarter">
        <div class="field is-grouped">
          <p class="control">
            <label class="label" for="state">
              State:
              <div class="select is-small">
                <select id="state" name="state" @change="changeState" v-model="selectedState">
                  <option v-for="state in states" :key="state" :value="state.state_id">
                    {{state.state_name}}
                  </option>
                </select>
              </div>
            </label>
          </p>
        </div>
      </div>
      <div class="column is-three-quarters">
        <div class="field is-grouped">
          <p class="control">
            <label class="label" for="district">
              District:
              <div class="select is-small">
                <select id="district" name="district" @change="changeDistrict" v-model="selectedDistrict" ref="district">
                  <option v-for="district in districts" :key="district" :value="district.district_id">
                    {{district.district_name}}
                  </option>
                </select>
              </div>
            </label>
          </p>
        </div>
      </div>
    </div>


    <div class="field is-grouped" v-show="optionType === 'pin'">
      <p class="control">
        <label class="label" for="age">
          PIN Code:
         <input class="is-small" type="text" name="pin" v-model="pinCode">
        </label>
      </p>
    </div>

    <div v-if="errorVal" class="has-text-danger card">
      <div v-html="errorVal"></div>
    </div>


    <div class="field is-grouped">
      <p class="control">
        <label class="label" for="vaccineType">
          Vaccine:
          <div class="select is-small">
            <select name="vaccineType" id="vaccineType" v-model="vaccineType" @change="getVaccinationInfoWeek">
              <option value="any">Any</option>
              <option value="COVISHIELD">COVISHIELD</option>
              <option value="COVAXIN">COVAXIN</option>
            </select>
          </div>
        </label>
      </p>
      <p class="control">
        <label class="label" for="dose">
          Dose:
          <div class="select is-small">
            <select name="dose" id="dose" v-model="dose" @change="getVaccinationInfoWeek">
              <option value="all">All</option>
              <option value="D1">1st Dose - D1</option>
              <option value="D2">2nd Dose - D2</option>
            </select>
          </div>
        </label>
      </p>
    </div>


    <div class="field is-grouped mt-3">
      <p class="control">
        <label class="label" for="age">
          Age limit:
          <div class="select is-small">
            <select name="age" id="age" v-model="age" @change="getVaccinationInfoWeek">
              <option value="any">Any</option>
              <option value="18">18-39 (18+)</option>
              <option value="40">40-44 (40+)</option>
              <option value="45">45+</option>
            </select>
          </div>
        </label>
      </p>
      <p class="control">
        <label class="checkbox">
          <input type="checkbox" v-model="notify.voice">
          Voice notify
        </label>
      </p>
    </div>

    <div class="field is-grouped">
      <div class="control">
        <input type="button" class="button is-primary is-small is-rounded" value="Get data" ref="getButton" @click="speak(true); getVaccinationInfoWeek()">
      </div>
      <div class="control">
        <button type="submit" class="button is-info is-small is-rounded" value="Poll" ref="pollButton" @click="startTimer">{{pollButtonText}}</button>
      </div>
      <div>
        <a href="https://selfregistration.cowin.gov.in/" target="_blank" class="button is-light is-link is-small is-rounded">Book in CoWin</a>
      </div>
    </div>


    <div class="columns is-gapless">
      <div class="column is-one-quarter">
        <span class="has-text-weight-bold is-uppercase has-text-danger" v-if="optionType"> {{ optionType }} </span>
        <span v-if="optionType === 'state'"> :
          <span class="has-text-weight-bold has-text-danger" v-if="selectedState"> {{ getName('state') }} </span> and
          <span class="has-text-weight-bold has-text-danger" v-if="selectedDistrict"> {{ getName('district') }} </span>
        </span>
        <span v-else>
          <span v-if="pinCode">: {{ pinCode }}</span>
        </span>
      </div>
      <div class="column is-offset-8">
        For
        Age [<span class="has-text-weight-bold has-text-danger" v-if="age"> {{ageRange[age]}} </span>]
        Vaccine [<span class="has-text-weight-bold has-text-danger" v-if="vaccineType"> {{vaccineType}} </span>]
        Dose [<span class="has-text-weight-bold has-text-danger" v-if="dose"> {{dose}} </span>]
      </div>
    </div>

    <!-- <div>
      <table class="table">
        <tr>
          <td v-for="(day, i) in sevenDaysFromToday" :key="i"> <span class="tag"> {{ day }} </span> </td>
        </tr>
      </table>
    </div> -->

    <div v-if="processedVaccInfoWeek && processedVaccInfoWeek.length > 0">
      <div class="is-size-6 has-text-weight-bold has-background-success" v-if="textToSpeak">{{textToSpeak}}</div>
      <InfoTable :vaccinationInfo="processedVaccInfoWeek" :isFiltered="true" :ageRange="ageRange" :selectedAge="age" :selectedVaccine="vaccineType" :selectedDose="dose"></InfoTable>
    </div>
    <div v-else>
      <div class="is-size-6 has-text-weight-bold has-background-danger">- No slots available -</div>
    </div>
    <br>
    <hr>

    <!-- <div v-if="vaccInfoWeek">
      <h1 class="is-size-5 has-text-weight-bold">All slots for all age groups</h1>
      <InfoTable :vaccinationInfo="vaccInfoWeek" :isFiltered="false" :ageRange="ageRange" ></InfoTable>
    </div> -->

  </div>
</template>

<script>
import axios from 'axios'
import InfoTable from '../components/InfoTable.vue';

export default {
  name: 'Home',
  components: {
    InfoTable
  },
  data() {
    return {
      optionType: '',
      age: 'any',
      ageRange: {
        '18': "18-39",
        '40': "40-44",
        '45': "45+",
        'any': 'all'
      },
      vaccineType: 'any',
      dose: 'all',
      date: null,
      formattedDate: null,
      datePickerDate: null,
      pinCode: null,
      states: null,
      districts: null,
      errorVal: null,
      selectedState: null,
      selectedDistrict: null,
      vaccInfoWeek: null,
      vaccinationInfo: null,
      processedVaccInfo: null,
      processedVaccInfoWeek: null,
      sevenDaysFromToday: [],
      initialCountVal: 5,
      count: this.initialCountVal,
      timer: null,
      isPolling: false,
      pollButtonText: "Get data automatically",
      textToSpeak: "Available in ",
      speakMsg: null,
      notify: {
        voice: true,
        popup: false
      },
    }
  },
  mounted() {
    this.$nextTick(function () {
      const convertedDate = new Date()
      this.date = this.getDateString(convertedDate);
      this.getNext7DaysFrom(convertedDate);
      this.getStates('mounted');

      if (this.notify.voice) {
        localStorage.setItem("voiceEnabled", this.notify.voice)
      }

      if (localStorage.pin) {
        this.pinCode = localStorage.pin;
      }

      if (localStorage.age) {
        this.age = localStorage.age;
      }

      if (localStorage.dose) {
        this.dose = localStorage.dose;
      }

      if (localStorage.vaccine) {
        this.vaccineType = localStorage.vaccine;
      }

      if (localStorage.option) {
        this.optionType = localStorage.option;
      }

      if (localStorage.voiceEnabled) {
        this.notify.voice = localStorage.voiceEnabled;
      }

      if ('speechSynthesis' in window && this.notify.voice) {
      // Synthesis support. Make your web apps talk!
        this.speakMsg = new SpeechSynthesisUtterance();
        this.speakMsg.lang = 'hi';
      }
    })
  },
  methods: {
    notifyMe() {
      var that = this;
      // Let's check if the browser supports notifications
      if (!("Notification" in window)) {
        alert("This browser does not support desktop notification");
      }
      // Let's check whether notification permissions have already been granted
      else if (Notification.permission === "granted") {
        // If it's okay let's create a notification
        new Notification(that.textToSpeak);
      }
      // Otherwise, we need to ask the user for permission
      else if (Notification.permission !== "denied") {
        Notification.requestPermission().then(function (permission) {
          // If the user accepts, let's create a notification
          if (permission === "granted") {
            new Notification(that.textToSpeak);
          }
        });
      }
    },
    getDateString(date) {
      return `${date.getFullYear()}-${String(date.getMonth() + 1)}-${date.getDate()}`;
    },
    getReadableDate(date) {
      const month = date.toLocaleString('default', { month: 'long' });
      return `${date.getDate()} ${month}`
    },
    getNext7DaysFrom(date) {
      if (date) {
        for (let i = 0; i < 7; i++) {
          var newDate = new Date();
          newDate.setDate(date.getDate() + i)
          this.sevenDaysFromToday.push(this.getReadableDate(newDate))
        }
      }
    },
    speak(flag) {
      (this.speakMsg && flag) ? speechSynthesis.speak(this.speakMsg) : speechSynthesis.cancel();
    },
    resetValues() {
      this.districts = null;
      this.selectedDistrict = null;
      this.processedVaccInfoWeek = null;
      this.vaccInfoWeek = null;
    },
    populateStoredValues() {
      if (this.optionType === 'state') {
        if (localStorage.state) {
          this.selectedState = localStorage.state;
          this.changeState();
        }

        if (localStorage.district) {
          this.selectedDistrict = localStorage.district;
          this.changeDistrict();
        }
      }

      if (localStorage.pin) {
        this.pinCode = localStorage.pin;
      }

      if (localStorage.option) {
        this.optionType = localStorage.option;
      }
    },
    changeOption() {
      this.errorVal = null;
      if (this.optionType) {
        localStorage.setItem("option", this.optionType);
      }
      this.populateStoredValues();
    },
    startTimer() {
      console.log("Poll button clicked");
      if (this.pollButtonText === "Get data automatically") {
        console.log("Timer started");
        this.timer = setInterval(() => this.countdown(), 1000);
        this.isPolling = true;
        this.pollButtonText = `Getting data... Click to stop`
      } else {
        clearInterval(this.timer);
        this.timer = null;
        this.pollButtonText = "Get data automatically"
        this.isPolling = false;
        if (this.notify.voice) {
          this.speak(false);
        }
      }
    },
    countdown() {
      if (this.count > 0) {
        this.count--
        console.log("Still counting...");
        this.pollButtonText = `Getting data in ${this.count}... Click to stop`
      } else {
        this.count = this.initialCountVal;
        if (this.notify.voice) {
          this.speak(true);
        }
        this.getVaccinationInfoWeek();
      }
    },
    feeType(type) {return type === 'Paid' ? 'lightpink' : 'lightgreen'},
    formatDate() {
      const convertedDate = new Date(this.date);
      this.formattedDate = `${convertedDate.getDate()}-${convertedDate.getMonth() + 1}-${convertedDate.getFullYear()}`
    },
    getName(item) {
      var arrayList, selectedItem, idKey = `${item}_id`, nameKey = `${item}_name`;
      if (item === 'state') {
        arrayList = this.states;
        selectedItem = this.selectedState;
      } else {
        arrayList = this.districts;
        selectedItem = this.selectedDistrict;
      }

      try {
        return arrayList.find(x => x[idKey].toString() === selectedItem.toString())[nameKey];
      } catch (error) {
        return null;
      }
    },
    changeState(event) {
      this.resetValues();
      localStorage.setItem('state', this.selectedState);
      this.getDistricts(event);
    },
    async getDistricts(event) {
      var that = this;
      await axios.get(`https://cdn-api.co-vin.in/api/v2/admin/location/districts/${this.selectedState}`)
      .then(function (response) {
        that.districts = response.data.districts
      })
      .catch(function (error) {
        that.errorVal = error.response
      });

      localStorage.setItem("districts", JSON.stringify(that.districts));

      if (localStorage.district) {
        this.selectedDistrict = localStorage.district;
        this.changeDistrict(event);
      }
    },
    changeDistrict(event) {
      if (event) {
        if ((event === 'mounted') || ((event !== 'mounted') && (event.type === 'change' && event.target.id === 'district') )) {
          localStorage.setItem('district', this.selectedDistrict);
          this.getVaccinationInfoWeek();
        }
      }
    },
    async getStates(event) {
      var that = this;
      await axios.get("https://cdn-api.co-vin.in/api/v2/admin/location/states")
      .then(function (response) {
        that.states = response.data.states
        localStorage.setItem("states", JSON.stringify(that.states));
      })
      .catch(function (error) {
        that.errorVal = error.response.error
        console.log(that.errorVal);
      });

      if (localStorage.state) {
        this.selectedState = localStorage.state;
        this.changeState(event);
      }
    },
    async getVaccinationInfoWeek() {
      this.errorVal = null;
      if (this.pinCode) {
        localStorage.setItem('pin', this.pinCode);
      }

      if (this.age) {
        localStorage.setItem('age', this.age);
      }

      if (this.dose) {
        localStorage.setItem('dose', this.dose)
      }

      if (this.vaccineType) {
        localStorage.setItem('vaccine', this.vaccineType)
      }

      this.formatDate();
      var that = this;
      let queryStr = null;
      if (this.optionType === 'state') {
        queryStr = `https://cdn-api.co-vin.in/api/v2/appointment/sessions/public/calendarByDistrict?district_id=${this.selectedDistrict}&date=${this.formattedDate}`
      } else {
        // const pincodes = ['110011','670303']
        queryStr = `https://cdn-api.co-vin.in/api/v2/appointment/sessions/public/calendarByPin?pincode=${this.pinCode}&date=${this.formattedDate}`
      }

      await axios.get(queryStr)
      .then(function (response) {
        if ('data' in response) {
          that.vaccInfoWeek = response.data.centers;
        } else {
          console.error(response)
          that.errorVal = response
        }

        that.processVaccinationInfoWeek();
      })
      .catch(function (error) {
        if ('response' in error && 'data' in error.response) {
          that.errorVal = error.response.data
          console.log(that.errorVal);
        } else {
          that.errorVal = error
        }
      });
    },
    getVaccinationInfo() {
      this.errorVal = null;
      this.vaccinationInfo = null

      var that = this;
      let queryStr = null;
      if (this.optionType === 'state') {
        queryStr = `https://cdn-api.co-vin.in/api/v2/appointment/sessions/public/findByDistrict?district_id=${this.selectedDistrict}&date=${this.formattedDate}`
      } else {
        queryStr = `https://cdn-api.co-vin.in/api/v2/appointment/sessions/public/findByPin?pincode=${this.pinCode}&date=${this.formattedDate}`
      }

      axios.get(queryStr)
      .then(function (response) {
        if ('data' in response) {
          that.vaccinationInfo = response.data.sessions
          that.processVaccinationInfo()
        } else {
          console.error(response)
          that.errorVal = response
        }
      })
      .catch(function (error) {
        console.log(error);
        if ('response' in error && 'data' in error.response) {
          that.errorVal = error.response.data.error
          console.log(that.errorVal);
        }
      });

    },
    processVaccinationInfo() {
      if (this.vaccinationInfo) {
        this.processedInfo = this.vaccinationInfo.filter((item) => {
          return item.available_capacity > 0
        });
      }
    },
    processVaccinationInfoWeek() {
      var that = this;
      var availableCenters = [];
      if (this.vaccInfoWeek) {
        this.processedVaccInfoWeek = this.vaccInfoWeek.filter((center) => {
          var sessInfo =  center.sessions.filter((sessionInfo) => {
              if(sessionInfo.available_capacity > 0) {
                let flags = [];
                let selectedAge = that.age.toString();

                if ( (isNaN(that.age) && selectedAge === 'any')
                    || (sessionInfo.min_age_limit.toString() === selectedAge)) {
                  flags.push(true);
                }

                if ( (this.vaccineType === 'any')
                    || (sessionInfo.vaccine === this.vaccineType) ) {
                  flags.push(true);
                }

                if (this.dose === 'all') {
                  flags.push(true);
                } else {
                  if (this.dose === 'D1') {
                    flags.push(sessionInfo.available_capacity_dose1 > 0);
                  } else if (this.dose === 'D2') {
                    flags.push(sessionInfo.available_capacity_dose2 > 0);
                  }
                }

                if (flags.every(x => x) && flags.length === 3) { // 3 Filters
                  availableCenters.push(center.name);
                  return true;
                }
              }
            });

            return (sessInfo.length > 0)
        });
      }

      availableCenters = [...new Set(availableCenters)];
      let availableText = availableCenters.length > 0 ? "Vaccination available in " + availableCenters[0] + (availableCenters.length > 1 ? ` and ${availableCenters.length - 1} other centers` : "") : "";
      this.textToSpeak = availableText;
      if(this.speakMsg) this.speakMsg.text = availableText;
    }
  }
}
</script>

<style scoped>

</style>