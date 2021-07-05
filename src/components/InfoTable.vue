<template>
    <table class="table is-bordered is-striped">
      <tbody>
        <tr v-for="item in vaccinationInfo" :key="item">
          <td>
            <div> <strong>{{item.name}}</strong> </div>
              {{item.address}}, {{item.pincode}}
              <span  class="tag is-uppercase" :class="item.fee_type === 'Paid' ? 'is-danger' : 'is-success'">
                {{item.fee_type}}
              </span>
          </td>
          <template v-for="sessionItem in item.sessions" >
            <td v-if="displayCondition(sessionItem)" :key="sessionItem.session_id">
              <div v-if="sessionItem.available_capacity > 0">
                <div class="tag mr-2 mb-2"><strong>{{ getReadableDate(sessionItem.date) }}</strong> </div> <br>
                <span class="tag is-uppercase is-info mr-2 mb-2">{{ sessionItem.vaccine }}</span>
                <span class="tag is-rounded is-warning">{{ageRange[sessionItem.min_age_limit.toString()]}}</span>
                <div>

                  <div class="field is-grouped is-grouped-multiline">
                    <div class="control" >
                      <div class="tags has-addons" v-if="sessionItem.available_capacity_dose1 > 0">
                        <span class="tag is-dark">Dose 1</span>
                        <span class="tag is-success">{{sessionItem.available_capacity_dose1}}</span>
                      </div>
                      <!-- <div v-else class="tags has-addons">
                        <span class="tag is-danger">Dose 1</span>
                        <span class="tag is-inverted">❌</span>
                      </div> -->
                    </div>
                    <div class="control">
                      <div class="tags has-addons" v-if="sessionItem.available_capacity_dose2">
                        <span class="tag is-dark">Dose 2</span>
                        <span class="tag is-success">{{sessionItem.available_capacity_dose2}}</span>
                      </div>
                      <!-- <div v-else class="tags has-addons">
                        <span class="tag is-danger">Dose 2</span>
                        <span class="tag is-inverted">❌</span>
                      </div> -->
                    </div>
                  </div>
                </div>
              </div>
              <div v-else class="tag is-uppercase is-danger">BOOKED</div>
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
    getReadableDate(inputDate) {
      let intDate = inputDate.split("-");
      let date = new Date(`${intDate[2]}-${intDate[1]}-${intDate[0]}`);
      const month = date.toLocaleString('default', { month: 'short' });
      const currDate = new Date();
      return `${date.getDate()} ${month} ${currDate.getFullYear() !== date.getFullYear() ? date.getFullYear() : ''}`;
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