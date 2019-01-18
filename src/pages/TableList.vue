<template>
<div class="content">

<div>

</div>
    <div class="md-layout">
    <div class="md-layout-item md-medium-size-100 md-xsmall-size-100 md-size-100">

        <md-card>
            <md-card-content>
            <div class="md-layout">

                 <div class="md-layout-item md-small-size-50 md-size-33">
                        <md-field>
                          <label>Enter BNF Code</label>
                          <md-input v-model="bnfcode" v-on:keyup.enter="getSpending" ></md-input>
                        </md-field>
                 </div>
                  <div class="md-layout-item md-small-size-50 md-size-33">
                    <div class="md-layout-item md-size-100 text-left">
                        <md-button v-on:click="getSpending" class="md-raised md-success">Get Spending Data</md-button>
                    </div>
                  </div>

            </div>

            <div class="md-layout-item md-small-size-100 md-size-33">
                <strong>Example codes</strong>
                <p><span class="example-code-name">Tradorec</span><input class="example-code-code" value="040702040BI" disabled><button v-on:click="loadExample('040702040BI')"> > </button></p>
                <p><span class="example-code-name">Tadalafil</span><input class="example-code-code" value="0704050R0" disabled><button v-on:click="loadExample('0704050R0')"> > </button></p>
                <p><span class="example-code-name">Antimotility</span><input class="example-code-code" value="010402" disabled><button v-on:click="loadExample('010402')"> > </button></p>
                <p><span class="example-code-name">Penicillins</span><input class="example-code-code" value="050101" disabled><button v-on:click="loadExample('050101')"> > </button></p>
             </div>
            </md-card-content>
        </md-card>
         </div>
    </div>


 <div class="md-layout">

      <div class="md-layout-item md-medium-size-33 md-xsmall-size-100 md-size-33" v-if="records.length > 0">
        <md-card>
          <md-card-header data-background-color="red">
            <h4 class="title">BNF Items</h4>
            <p class="category"></p>
          </md-card-header>
          <md-card-content>
            <div class="row">
                <ul v-for="(name, index) in bnfnames" :key="index">
                    <li>{{name.name}}</li>
                </ul>
            </div>
          </md-card-content>
        </md-card>
      </div>

      <div class="md-layout-item md-medium-size-100 md-xsmall-size-100 md-size-66" v-if="records.length > 0">
            <chart-card
              :chart-data="prescriptionsChart.data"
              :chart-options="prescriptionsChart.options"
              :chart-responsive-options="prescriptionsChart.responsiveOptions"
              :chart-type="'Line'"
              :key="uniqueKey"
              data-background-color="red">
              <template slot="content">
                <h4 class="title">Total Monthly Prescriptions (past 12 months)</h4>

              </template>


            </chart-card>

           <md-card>
              <md-card-header data-background-color="orange">
                <h4 class="title">Spending by BNF Code</h4>
                <p class="category">Total NHS prescribing spending</p>
              </md-card-header>
              <md-card-content>
                <simple-table table-header-color="yellow" :data="records"></simple-table>
              </md-card-content>
            </md-card>
      </div>
    </div>

  </div>
</template>

<script>
import axios from "axios";

import { SimpleTable, ChartCard } from "@/components";

export default {
  components: {
    SimpleTable,
    ChartCard
  },
  data() {
    return {
      uniqueKey: 0,
      records: [],
      bnfnames: [],
      loading: false,
      bnfcode: "",
      prescriptionsChart: {
        data: {
          labels: [
            "Ja",
            "Fe",
            "Ma",
            "Ap",
            "Mai",
            "Ju",
            "Jul",
            "Au",
            "Se",
            "Oc",
            "No",
            "De"
          ],
          series: [[]]
        },
        options: {
          axisX: {
            showGrid: false
          },
          low: 0,
          high: 0,
          chartPadding: {
            top: 0,
            right: 5,
            bottom: 0,
            left: 0
          }
        },
        responsiveOptions: [
          [
            "screen and (max-width: 640px)",
            {
              seriesBarDistance: 5,
              axisX: {
                labelInterpolationFnc: function(value) {
                  return value[0];
                }
              }
            }
          ]
        ]
      }
    };
  },
  mounted: function() {
    //  Load example BNF Code for development
    //this.bnfcode = "040702040BI";
    //this.getSpending();
  },
  methods: {
    getSpending: function() {
      this.loading = true;
      axios
        .get(
          "https://openprescribing.net/api/1.0/bnf_code/?q=" +
            this.bnfcode +
            "&format=json"
        )
        .then(
          response => {
            this.loading = false;
            this.bnfnames = response.data;
          },
          error => {
            console.log(error);
            this.loading = false;
          }
        );
      axios
        .get(
          "https://openprescribing.net/api/1.0/spending/?code=" +
            this.bnfcode +
            "&format=json"
        )
        .then(
          response => {
            this.loading = false;
            this.records = response.data;
            this.prescriptionsChart.data.series = [[]];

            let lastTwelveMonths = this.records.slice(this.records.length - 12).map((x) => x.items);
            console.log(lastTwelveMonths)
            //console.log(lastTwelveMonths.map((x) => x.items));
            this.prescriptionsChart.data.series = [lastTwelveMonths];
            this.prescriptionsChart.options.high = Math.max(lastTwelveMonths);
            this.uniqueKey++;
          },
          error => {
            console.log(error);
            this.loading = false;
          }
        );
    },

    loadExample: function(exampleCode) {
      this.bnfcode = exampleCode;
      this.getSpending();
    }
  }
};
</script>

<style>
.example-code-name {
  padding-right: 20px;
  display: inline-block;
  width: 80px;
}
</style>
