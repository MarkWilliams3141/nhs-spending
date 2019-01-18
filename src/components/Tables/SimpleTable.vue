<template>
  <div>
    <md-table v-model="data" :table-header-color="tableHeaderColor">
      <md-table-row slot="md-table-row" slot-scope="{ item }">
        <md-table-cell md-label="Date (month)">{{ item.date }}</md-table-cell>
        <md-table-cell md-label="Items">{{ item.items }}</md-table-cell>
        <md-table-cell md-label="Actual Cost (£)">{{ item.actual_cost }}</md-table-cell>
        <md-table-cell md-label="Quantity">{{ item.quantity }}</md-table-cell>
      </md-table-row>
    </md-table>
  </div>
</template>

<script>
export default {
  name: "simple-table",
  props: {
    tableHeaderColor: {
      type: String,
      default: ""
    },
    data: {
      type: Array
    }
  },
  data() {
    return {
      selected: [],
      users: [
        {
          name: "Dakota Rice",
          salary: "$36,738",
          country: "Niger",
          city: "Oud-Turnhout"
        }
      ]
    };
  }
};
</script>
