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
          <td v-for="sessionItem in item.sessions" :key="sessionItem">
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
        </tr>
      </tbody>
  </table>
</template>

<script>
export default {
  name: 'InfoTable',
  props: {
    vaccinationInfo: Object
  },
  methods: {
    isAvailable(availability) {return availability > 0 ? 'lightgreen' : 'lightpink'},
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

/* table {
  text-align: center;
  border-bottom: 1px solid black;
}

td, th {
  border: 1px solid black;
  border-collapse: collapse;
  margin: 5px;
  padding: 5px;
} */


</style>