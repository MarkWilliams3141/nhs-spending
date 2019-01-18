<template>
  <div class="content">

<div>

</div>
    <div class="md-layout">
    <div class="md-layout-item md-medium-size-100 md-xsmall-size-100 md-size-100">


        <md-card>
            <md-card-content>
            <div class="md-layout">

                 <div class="md-layout-item md-small-size-100 md-size-33">
                        <md-field>
                          <label>Enter BNF Code</label>
                          <md-input v-model="bnfcode" v-on:keyup.enter="getSpending" ></md-input>
                        </md-field>
                 </div>
                  <div class="md-layout-item md-small-size-100 md-size-33">
                    <div class="md-layout-item md-size-100 text-left">
                        <md-button v-on:click="getSpending" class="md-raised md-success">Get Spending Data</md-button>
                    </div>
                  </div>

            </div>

            <div class="md-layout-item md-small-size-100 md-size-33">
                <strong>Example codes</strong>
                <p><span class="example-code-name">Tradorec</span><input class="example-code-code" value="040702040BI"><button v-on:click="loadExample('040702040BI')"> > </button></p>
                <p><span class="example-code-name">Tadalafil</span><input class="example-code-code" value="0704050R0"><button v-on:click="loadExample('0704050R0')"> > </button></p>
                <p><span class="example-code-name">Antimotility</span><input class="example-code-code" value="010402"><button v-on:click="loadExample('010402')"> > </button></p>
                <p><span class="example-code-name">Penicillins</span><input class="example-code-code" value="050101"><button v-on:click="loadExample('050101')"> > </button></p>
             </div>
            </md-card-content>
        </md-card>
         </div>
    </div>


 <div class="md-layout">

      <div class="md-layout-item md-medium-size-25 md-xsmall-size-100 md-size-25"  v-if="records.length > 0">
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


      <div class="md-layout-item md-medium-size-75 md-xsmall-size-100 md-size-75"  v-if="records.length > 0">
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

import { SimpleTable } from "@/components";

export default {
  components: {
    SimpleTable
  },
  data() {
    return {
      records: [],
      bnfnames: [],
      loading: false,
      bnfcode: ""
    };
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
