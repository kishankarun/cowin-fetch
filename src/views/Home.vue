<template>
  <div class="home">
    <p class="is-italic has-text-danger">* Works only in Desktop browsers and Android chrome browser.</p>

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

    <div class="field is-grouped">
    <p class="control">
      <label class="label">
        From date:
        <input type="date" id="date" name="date" v-model="date" @change="formatDate">
      </label>
    </p>
    </div>


    <div class="field is-grouped" v-show="!errorVal && optionType === 'state'">
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


    <div class="field is-grouped" v-show="optionType === 'pin'">
      <p class="control">
        <label class="label" for="age">
          PIN Code:
         <input class="is-small" type="text" name="pin" v-model="pinCode">
        </label>
      </p>
    </div>

    <div v-if="errorVal" class="has-text-danger">
      {{errorVal}}
    </div>

    <div class="field is-grouped">
      <p class="control">
        <label class="label" for="age">
          Age limit:
          <div class="select is-small">
            <select name="age" id="age" v-model="age" @change="getVaccinationInfoWeek">
              <option value="18">18+</option>
              <option value="40">40+</option>
              <option value="45">45+</option>
            </select>
          </div>
        </label>
      </p>
    </div>

    <div class="field is-grouped">
      <div class="control">
        <input type="button" class="button is-primary" value="Get data" ref="getButton" @click="getVaccinationInfoWeek">
      </div>
      <div class="control">
        <button type="submit" class="button is-info" value="Poll" ref="pollButton" @click="startTimer">{{pollButtonText}}</button>
      </div>
    </div>

    <h1 class="subtitle">
      <span class="is-uppercase has-text-danger" v-if="optionType"> {{ optionType }} </span>
      <span v-if="optionType === 'state'">
        <span v-if="selectedState"> :  {{ getName('state') }} </span>
        <span v-if="selectedDistrict"> and {{ getName('district') }} </span>
      </span>
      <span v-else>
        <span v-if="pinCode">: {{ pinCode }}</span>
      </span>
    </h1>

    <h1 class="title">Available slots <span class="has-text-weight-bold has-text-danger" v-if="age">for {{age}}+ age group</span></h1>

    <div v-if="processedVaccInfoWeek && processedVaccInfoWeek.length > 0">
      <table style="width: 100%; display: block; max-height: 300px; overflow-y: scroll">
        <tbody>
          <tr v-for="item in processedVaccInfoWeek" :key="item">
            <td>
                <h5>{{item.name}}</h5>
                <strong>Block:</strong> {{item.block_name}} <br>
              </td>
              <td>
                {{item.address}}, {{item.pincode}}
                <span v-if="item.fee_type === 'Paid'" style="text-transform: uppercase;color: white; background: #ad0000;">
                  {{item.fee_type}}
                </span>
              </td>
            <template v-for="sessionItem in item.sessions" >
              <td v-if="sessionItem.available_capacity" :key="sessionItem">
                <div> <strong>{{ sessionItem.date }}</strong> </div>
                <div> {{ sessionItem.vaccine }} </div>
                <div>
                  <small>Age: {{sessionItem.min_age_limit }}+</small>
                  <table border="1">
                    <thead>
                      <tr>
                        <th>D1</th><th>Total</th><th>D2</th>
                      </tr>
                    </thead>
                    <tr>
                      <td>{{sessionItem.available_capacity_dose1}}</td>
                      <td :style="{backgroundColor: isAvailable(sessionItem.available_capacity)}">{{sessionItem.available_capacity}}</td>
                      <td>{{sessionItem.available_capacity_dose2}}</td>
                    </tr>
                  </table>
                </div>
              </td>
            </template>
          </tr>
        </tbody>
      </table>
    </div>
    <div v-else>
      <h2 class="subtitle">No slots available</h2>
    </div>
    <br>
    <span class="label">D1: Dose 1, D2: Dose 2</span>
    <hr>

    <div v-if="vaccInfoWeek">
      <h1 class="title">All slots for all age groups</h1>
      <table style="width: 100%; display: block; max-height: 300px; overflow-y: scroll">
        <tr v-for="item in vaccInfoWeek" :key="item">
           <td>
              <h5>{{item.name}}</h5> </td>
            <td>
              <strong>Block:</strong> {{item.block_name}} <br>
              {{item.address}}, {{item.pincode}}
              <span v-if="item.fee_type === 'Paid'" style="text-transform: uppercase;color: white; background: #ad0000;">
                {{item.fee_type}}
              </span>
            </td>
          <td v-for="sessionItem in item.sessions" :key="sessionItem">
            <div> <strong>{{ sessionItem.date }}</strong> </div>
            <div> {{ sessionItem.vaccine }} </div>
            <div>
              <small>Age: {{sessionItem.min_age_limit }}+</small>
              <table>
                <tr>
                  <td><strong>D1</strong> <br>{{sessionItem.available_capacity_dose1}}</td>
                  <td :style="{backgroundColor: isAvailable(sessionItem.available_capacity)}">{{sessionItem.available_capacity}}</td>
                  <td>D2 <br>{{sessionItem.available_capacity_dose2}}</td>
                </tr>
              </table>
            </div>
          </td>
        </tr>
      </table>
    </div>

  </div>
</template>

<script>
import axios from 'axios'

export default {
  name: 'Home',
  components: {
  },
  data() {
    return {
      optionType: '',
      age: 18,
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
      initialCountVal: 5,
      count: this.initialCountVal,
      timer: null,
      isPolling: false,
      pollButtonText: "Get data automatically"
    }
  },
  mounted() {
    this.$nextTick(function () {
      const convertedDate = new Date()
      this.date = `${convertedDate.getFullYear()}-${String(convertedDate.getMonth() + 1).padStart(2, 0)}-${convertedDate.getDate()}`
      this.getStates('mounted');

      if (localStorage.pin) {
        this.pinCode = localStorage.pin;
      }

      if (localStorage.age) {
        this.age = localStorage.age;
      }

      if (localStorage.option) {
        this.optionType = localStorage.option;
      }
    })
  },
  methods: {
    notifyMe() {
      // Let's check if the browser supports notifications
      if (!("Notification" in window)) {
        alert("This browser does not support desktop notification");
      }
      // Let's check whether notification permissions have already been granted
      else if (Notification.permission === "granted") {
        // If it's okay let's create a notification
        new Notification("Hi there!");
      }
      // Otherwise, we need to ask the user for permission
      else if (Notification.permission !== "denied") {
        Notification.requestPermission().then(function (permission) {
          // If the user accepts, let's create a notification
          if (permission === "granted") {
            new Notification("Hi there!");
          }
        });
      }

      // At last, if the user has denied notifications, and you
      // want to be respectful there is no need to bother them any more.
    },
    resetValues() {
      this.districts = null;
      this.selectedDistrict = null;
      this.processedVaccInfoWeek = null;
      this.vaccInfoWeek = null;
    },
    populateStoredValues() {
      if (localStorage.state) {
        this.selectedState = localStorage.state;
        this.changeState();
      }

      if (localStorage.district) {
        this.selectedDistrict = localStorage.district;
        this.changeDistrict();
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
      }
    },
    countdown() {
      if (this.count > 0) {
        this.count--
        this.pollButtonText = `Getting data in ${this.count}... Click to stop`
      } else {
        this.count = this.initialCountVal;
        this.getVaccinationInfoWeek();
      }
    },
    isAvailable(availability) {return availability > 0 ? 'lightgreen' : 'lightpink'},
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
      if ((event === 'mounted') || ((event.type === 'change' && event.target.id === 'district') )) {
        localStorage.setItem('district', this.selectedDistrict);
        this.getVaccinationInfoWeek();
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
        that.errorVal = error.response.data.error
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
      this.formatDate();
      var that = this;
      let queryStr = null;
      if (this.optionType === 'state') {
        queryStr = `https://cdn-api.co-vin.in/api/v2/appointment/sessions/public/calendarByDistrict?district_id=${this.selectedDistrict}&date=${this.formattedDate}`
      } else {
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
        console.log(error);
        if ('response' in error && 'data' in error.response) {
          that.errorVal = error.response.data.error
          console.log(that.errorVal);
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
      if (this.vaccInfoWeek) {
        this.processedVaccInfoWeek = this.vaccInfoWeek.filter((center) => {
          var sessInfo =  center.sessions.filter((sessionInfo) => {
              if(sessionInfo.available_capacity > 0 && sessionInfo.min_age_limit.toString() === that.age.toString()) {
                return true;
              } else {
                return false;
              }
            })

            return (sessInfo.length > 0)
        });
      }

    }
  }
}
</script>

<style scoped>
table {
  text-align: center;
}

td, th {
  border: 1px solid black;
  border-collapse: collapse;
  margin: 5px;
  padding: 5px;
}


</style>