<template>
    <table class="table is-bordered is-striped">
      <tbody>
        <tr v-for="item in vaccinationInfo" :key="item">
          <td>
            <div> <strong>{{item.name}}</strong> </div>
              {{item.address}}, {{item.pincode}}
              <span v-if="item.fee_type === 'Paid'" style="text-transform: uppercase;color: white; background: #ad0000;">
                {{item.fee_type}}
              </span>
          </td>
          <template v-for="sessionItem in item.sessions" >
            <td v-if="displayCondition(sessionItem)" :key="sessionItem.session_id">
              <div> <strong>{{ sessionItem.date }}</strong> </div>
              <div> {{ sessionItem.vaccine }} </div>
              <div>
                <small>Age: {{ageRange[sessionItem.min_age_limit.toString()]}} </small>
                <table>
                  <tr>
                    <td><strong>D1 </strong> <br>{{sessionItem.available_capacity_dose1}}</td>
                    <td :style="{backgroundColor: isAvailable(sessionItem.available_capacity)}">{{sessionItem.available_capacity}}</td>
                    <td><strong>D2 </strong> <br>{{sessionItem.available_capacity_dose2}}</td>
                  </tr>
                </table>
              </div>
            </td>
          </template>
        </tr>
      </tbody>
  </table>
</template>

<script>
export default {
  name: 'InfoTable',
  props: {
    vaccinationInfo: Object,
    isFiltered: Boolean,
    selectedAge: String,
    ageRange: Object,
    selectedVaccine: String,
    selectedDose: String
  },
  data() {
    return {
    }
  },
  mounted() {
  },
  methods: {
    isAvailable(availability) {return availability > 0 ? 'lightgreen' : 'lightpink'},
    ageFilter(ageLimit) {
      return (this.selectedAge === "any") ? true : this.selectedAge.toString() === ageLimit.toString();
    },
    displayCondition(info) {
      return (!this.isFiltered)
              || (this.isFiltered
                  && (info.available_capacity > 0) && this.ageFilter(info.min_age_limit)
                  && (info.vaccine === this.selectedVaccine || this.selectedVaccine === 'any')     )
    },
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

</style>