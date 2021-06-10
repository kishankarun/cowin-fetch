<template>
  <div class="home">
    <!-- <img alt="Vue logo" src="../assets/logo.png"> -->
    <!-- <HelloWorld msg="Welcome to Your Vue.js App"/> -->

    <div>
      <select name="" id="" v-model="optionType">
        <option value="pin">PIN</option>
        <option value="state">State</option>
      </select>
    </div>

        <input type="date" id="date" name="date" v-model="date" @change="formatDate">

    <div v-if="optionType === 'state'">
      <div v-if="!errorVal">
        <select @change="getDistrict()" v-model="selectedState">
          <option v-for="state in states" :key="state" :value="state.state_id">
            {{state.state_name}}
          </option>
        </select>
        <select @change="changeDistrict()" v-model="selectedDistrict" ref="district">
          <option v-for="district in districts" :key="district" :value="district.district_id">
            {{district.district_name}}
          </option>
        </select>
      </div>
      <div v-else>
        {{errorVal}}
      </div>
    </div>
    <div v-else>
      <label for="pin">PIN Code: </label><input type="text" name="pin" v-model="pinCode">
    </div>
        <input type="button" value="Get" ref="getButton" @click="getVaccinationInfo">
        <input type="button" value="Poll" ref="pollButton" @click="startTimer">

        <input type="button" value="Get Calendar" ref="getButton" @click="getVaccinationInfoWeek">

    <div v-if="vaccInfoWeek">

      <table border="1">
        <tr v-for="item in vaccInfoWeek" :key="item">
          <!-- <td> {{item.date}} </td> --> <td>
            <!-- {{item.center_id}} :  --> <h5>{{item.name}}</h5> </td>
            <td>
              {{item.block_name}}, {{item.address}}, {{item.pincode}}
              <span v-if="item.fee_type === 'Paid'" style="text-transform: uppercase;color: white; background: #ad0000;">
                {{item.fee_type}}
              </span>
            </td>
          <!-- <td :style="{backgroundColor: isAvailable(item.available_capacity)}" > {{item.available_capacity}} </td>
          <td :style="{backgroundColor: isAvailable(item.available_capacity_dose1)}"> {{item.available_capacity_dose1}} </td>
          <td :style="{backgroundColor: isAvailable(item.available_capacity_dose2)}"> {{item.available_capacity_dose2}} </td> -->
          <!-- <td> {{item.from}} </td> <td> {{item.to}} </td> <td> {{item.min_age_limit }} </td> <td> {{item.vaccine}} </td> -->
          <!-- <td :style="{backgroundColor: feeType(item.fee_type)}"> {{item.fee_type}} </td> <td> {{item.fee}} </td> -->
          <!-- <td> {{item.slots}} </td> -->
          <td v-for="sessionItem in item.sessions" :key="sessionItem">
            <div> <strong>{{ sessionItem.date }}</strong> </div>
            <div> {{ sessionItem.vaccine }} </div>
            <div>
              <small>Age: {{sessionItem.min_age_limit }}+</small>
              <table style="border: 1px solid black; border-collapse: collapse;">
                <tr>
                  <td>D1 <br>{{sessionItem.available_capacity_dose1}}</td>
                  <td :style="{backgroundColor: isAvailable(sessionItem.available_capacity)}">{{sessionItem.available_capacity}}</td>
                  <td>D2 <br>{{sessionItem.available_capacity_dose2}}</td>
                </tr>
              </table>
            </div>

              <!-- <table border="1">
                <thead>
                  <tr>
                    <td>Date</td>
                    <td> available_capacity </td> <td> available_capacity_dose1 </td> <td> available_capacity_dose2 </td>
                  <td> from </td> <td> to </td>
                  <td> min_age_limit  </td> <td> vaccine </td>
                  <td> fee_type </td> <td> fee </td> <td> slots </td>
                  </tr>
                </thead>
                <tr v-for="sessionItem in item.sessions" :key="sessionItem">
                  <td> {{sessionItem.date}} </td>
                  <td :style="{backgroundColor: isAvailable(sessionItem.available_capacity)}" > {{sessionItem.available_capacity}} </td>
                  <td :style="{backgroundColor: isAvailable(sessionItem.available_capacity_dose1)}"> {{sessionItem.available_capacity_dose1}} </td>
                  <td :style="{backgroundColor: isAvailable(sessionItem.available_capacity_dose2)}"> {{sessionItem.available_capacity_dose2}} </td>
                  <td> {{sessionItem.from}} </td> <td> {{sessionItem.to}} </td>
                  <td> {{sessionItem.min_age_limit }} </td> <td> {{sessionItem.vaccine}} </td>
                  <td :style="{backgroundColor: feeType(sessionItem.fee_type)}"> {{sessionItem.fee_type}} </td> <td> {{sessionItem.fee}} </td>
                  <td> {{sessionItem.slots}} </td>
                </tr>
              </table> -->
          </td>
        </tr>
      </table>
    </div>
    <span v-else>
      <span v-if="selectedDistrict">Getting info of {{selectedDistrict}}</span>
    </span>



    <!-- <div v-if="vaccinationInfo">
      <h1>Availablility</h1>
      <table border="1">
        <thead>
          <tr>
            <td> date </td> <td>center_id : name </td><td> address </td> <td> block_name </td>
          <td> pincode </td> <td> available_capacity </td> <td> available_capacity_dose1 </td> <td> available_capacity_dose2 </td>
          <td> from </td> <td> to </td> <td> min_age_limit  </td> <td> vaccine </td> <td> fee_type </td> <td> fee </td>
          <td> slots </td>
          </tr>
        </thead>
        <tr v-for="item in processedInfo" :key="item">
          <td> {{item.date}} </td> <td>{{item.center_id}} : {{item.name}} </td><td> {{item.address}} </td> <td> {{item.block_name}} </td>
          <td> {{item.pincode}} </td>
          <td :style="{backgroundColor: isAvailable(item.available_capacity)}" > {{item.available_capacity}} </td>
          <td :style="{backgroundColor: isAvailable(item.available_capacity_dose1)}"> {{item.available_capacity_dose1}} </td>
          <td :style="{backgroundColor: isAvailable(item.available_capacity_dose2)}"> {{item.available_capacity_dose2}} </td>
          <td> {{item.from}} </td> <td> {{item.to}} </td> <td> {{item.min_age_limit }} </td> <td> {{item.vaccine}} </td>
          <td :style="{backgroundColor: feeType(item.fee_type)}"> {{item.fee_type}} </td> <td> {{item.fee}} </td>
          <td> {{item.slots}} </td>
        </tr>
      </table>

      <br><br>
      <hr>
      <h1>All data</h1>
      <table border="1">
        <thead>
          <tr>
            <td> date </td> <td>center_id : name </td><td> address </td> <td> block_name </td>
          <td> pincode </td> <td> available_capacity </td> <td> available_capacity_dose1 </td> <td> available_capacity_dose2 </td>
          <td> from </td> <td> to </td> <td> min_age_limit  </td> <td> vaccine </td> <td> fee_type </td> <td> fee </td>
          <td> slots </td>
          </tr>
        </thead>
        <tr v-for="item in vaccinationInfo" :key="item">
          <td> {{item.date}} </td> <td>{{item.center_id}} : {{item.name}} </td><td> {{item.address}} </td> <td> {{item.block_name}} </td>
          <td> {{item.pincode}} </td>
          <td :style="{backgroundColor: isAvailable(item.available_capacity)}" > {{item.available_capacity}} </td>
          <td :style="{backgroundColor: isAvailable(item.available_capacity_dose1)}"> {{item.available_capacity_dose1}} </td>
          <td :style="{backgroundColor: isAvailable(item.available_capacity_dose2)}"> {{item.available_capacity_dose2}} </td>
          <td> {{item.from}} </td> <td> {{item.to}} </td> <td> {{item.min_age_limit }} </td> <td> {{item.vaccine}} </td>
          <td :style="{backgroundColor: feeType(item.fee_type)}"> {{item.fee_type}} </td> <td> {{item.fee}} </td>
          <td> {{item.slots}} </td>
        </tr>
      </table>
    </div>
    <span v-else>
      <span v-if="selectedDistrict">Getting info of {{selectedDistrict}}</span>
    </span> -->
  </div>
</template>

<script>
// @ is an alias to /src
// import HelloWorld from '@/components/HelloWorld.vue'
import axios from 'axios'

export default {
  name: 'Home',
  components: {
    // HelloWorld
  },
  data() {
    return {
      optionType: 'state',
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
      processedInfo: null,
      initialCountVal: 5,
      count: this.initialCountVal,
      timer: null,
    }
  },
  mounted() {
    this.$nextTick(function () {
      const convertedDate = new Date()
      this.date = `${convertedDate.getFullYear()}-${String(convertedDate.getMonth() + 1).padStart(2, 0)}-${convertedDate.getDate()}`
      this.getStates();
    })
  },
  methods: {
    startTimer: function() {
      if (this.$refs.pollButton.value === "Poll") {
        console.log("Timer started", this.count);
        this.timer = setInterval(() => this.countdown(), 1000);
        this.resetButton = true;
        this.$refs.pollButton.value = "Stop"
      } else {
        clearInterval(this.timer);
        this.timer = null;
        this.$refs.pollButton.value = "Poll"
      }

    },
    countdown() {
      if (this.count > 0) {
        this.count--
        console.log("Counting down: ", this.count);
      } else {
        this.count = this.initialCountVal;
        // this.getVaccinationInfo();
        this.getVaccinationInfoWeek();
      }
    },
    isAvailable(availability) {return availability > 0 ? 'lightgreen' : 'lightpink'},
    feeType(type) {return type === 'Paid' ? 'lightpink' : 'lightgreen'},
    formatDate() {
      const convertedDate = new Date(this.date);
      console.log(convertedDate);
      this.formattedDate = `${convertedDate.getDate()}-${convertedDate.getMonth() + 1}-${convertedDate.getFullYear()}`
      console.log(convertedDate.getFullYear(), convertedDate.getMonth() + 1, convertedDate.getDate());
    },
    getDistrict() {
      console.log(this.selectedState);
      this.$refs.district.focus();
      var that = this;
      axios.get(`https://cdn-api.co-vin.in/api/v2/admin/location/districts/${this.selectedState}`)
      .then(function (response) {
        console.log(response.data)
        that.districts = response.data.districts
      })
      .catch(function (error) {
        that.errorVal = error.response
      });
    },
    changeDistrict() {
      this.formatDate()
      // this.getVaccinationInfo()
      this.getVaccinationInfoWeek()
    },
    getStates() {
      var that = this;
      axios.get("https://cdn-api.co-vin.in/api/v2/admin/location/states")
      .then(function (response) {
        console.log(response.data)
        that.states = response.data.states
      })
      .catch(function (error) {
        that.errorVal = error.response.data.error
        console.log(that.errorVal);
      });
    },
    getVaccinationInfoWeek() {
      var that = this;
      axios.get(`https://cdn-api.co-vin.in/api/v2/appointment/sessions/public/calendarByDistrict?district_id=${this.selectedDistrict}&date=${this.formattedDate}`)
      .then(function (response) {
        if ('data' in response) {
          console.log(response.data)
          that.vaccInfoWeek = response.data.centers;
          // that.vaccinationInfo = response.data.sessions
          // that.processVaccinationInfo()
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
    getVaccinationInfo() {
      this.vaccinationInfo = null

      var that = this;
      let queryStr = null;
      if (this.optionType === 'state') {
        queryStr = `https://cdn-api.co-vin.in/api/v2/appointment/sessions/public/findByDistrict?district_id=${this.selectedDistrict}&date=${this.formattedDate}`
      } else {
        queryStr = `https://cdn-api.co-vin.in/api/v2/appointment/sessions/public/findByPin?pincode=${this.pinCode}&date=${this.formattedDate}`
      }

      console.log(queryStr);
      axios.get(queryStr)
      .then(function (response) {
        if ('data' in response) {
          console.log(response.data)
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
    }
  }
}
</script>

<style scoped>
table, td, th {
  border: 1px solid black;
  border-collapse: collapse;
  margin: 5px;
  padding: 5px;
}


</style>