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
            <!-- {{sessionItem}} -->
            <td v-if="!isFiltered" :key="sessionItem.session_id">
              <div> <strong>{{ sessionItem.date }}</strong> </div>
              <div> {{ sessionItem.vaccine }} </div>
              <div>
                <small>Age: {{sessionItem.min_age_limit }}+</small>
                <table>
                  <tr>
                    <td><strong>D1 </strong> <br>{{sessionItem.available_capacity_dose1}}</td>
                    <td :style="{backgroundColor: isAvailable(sessionItem.available_capacity)}">{{sessionItem.available_capacity}}</td>
                    <td><strong>D2 </strong> <br>{{sessionItem.available_capacity_dose2}}</td>
                  </tr>
                </table>
              </div>
            </td>
            <td v-else-if="isFiltered && sessionItem.available_capacity > 0" :key="sessionItem.session_id">
              <div> <strong>{{ sessionItem.date }}</strong> </div>
              <div> {{ sessionItem.vaccine }} </div>
              <div>
                <small>Age: {{sessionItem.min_age_limit }}+</small>
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
    isFiltered: Boolean
  },
  methods: {
    isAvailable(availability) {return availability > 0 ? 'lightgreen' : 'lightpink'},
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

</style>