<template>
  <div id="app">
    <h1 style="text-align: center; margin-top:150px; margin-bottom: 80px">Quality of Life</h1>
    <div>
      <div>
        <b-container class="bv-example-row">
          <b-row>
            <b-col>
              <b-form-group>
                <template slot="label">
                  <br>
                  <b-form-checkbox
                    style
                    v-model="allSelected"
                    aria-describedby="flavours"
                    aria-controls="flavours"
                    @change="toggleAll"
                  >{{ allSelected ? 'Un-select All' : 'Select All' }}</b-form-checkbox>
                </template>
                <br/>
                <b-form-checkbox-group
                  id="flavors"
                  v-model="selected"
                  name="flavors"
                  class="ml-5"
                  aria-label="Individual flavours"
                  stacked
                >
                  <b-form-checkbox
                    v-for="Automations in Automation"
                    v-bind:key="Automations.id"
                    :value="Automations"
                    class="mb-5"
                  >{{ Automations }}</b-form-checkbox>
                </b-form-checkbox-group>

              </b-form-group>
            </b-col>

            <b-col>
              <div style="margin-top: 85px">
                <progress-bar
                  style="margin-bottom: 35px; width: 95%"
                  size="large"
                  text-position="inside"
                  :val="increasing_pct_Ecom"
                  :text="increasing_pct_Ecom == 100 ? completedPerc: increasing_pct_Ecom + '%' "

                />

                <progress-bar
                  style="margin-bottom: 35px; width: 95%"
                  size="large"
                  text-position="inside"
                  :val="increasing_pct_Real"

                  :text="increasing_pct_Real == 100 ? completedPerc : increasing_pct_Real+ '%' "


                />

                <progress-bar
                  style="margin-bottom: 45px; width: 95%"
                  size="large"
                  text-position="inside"
                  :val="increasing_pct_PnA"

                  :text="increasing_pct_PnA == 100 ? completedPerc : increasing_pct_PnA+ '%' "



                />

                <progress-bar
                  size="large"
                  style="width: 95%"
                  text-position="inside"
                  :val="increasing_pct_Vertex"

                  :text="increasing_pct_Vertex == 100 ? completedPerc : increasing_pct_Vertex+ '%' "



                />
              </div>
            </b-col>
          </b-row>
        </b-container>
      </div>
    </div>

<div>

</div>

    <b-button
      variant="primary"
      style="margin-left: 80px; width:40%"
      size="lg"
      @click="boxOne===true?toStart():showModal()"

    >Run Test</b-button>
    <b-button
      variant="danger"
      style="margin-left: 60px; width: 40%"
      size="lg"
      v-on:click="toStop()"
    >Stop Test</b-button>
  </div>
</template>

<script>
import ProgressBar from "vue-simple-progress";
import { setInterval } from "timers";
import { clearInterval } from "timers";

import BootstrapVue from "bootstrap-vue";


function initialState() {
  return {
    increasing_pct_Ecom: 0,
    increasing_pct_Real: 0,
    increasing_pct_PnA: 0,
    increasing_pct_Vertex: 0,
    boxOne: ''
  };
}
export default {
  name: "app",
  data() {
    return {
      Automation: [
        "ECommerce SRT",
        "Real Time Pricing (RTP)",
        "Pricing and Availability (PnA)",
        "Vertex"
      ],
      selected: [],
      allSelected: false,
      random_pct: 0,
      increasing_pct_Ecom: 0,
      increasing_pct_Real: 0,
      increasing_pct_PnA: 0,
      increasing_pct_Vertex: 0,
      vertex_cond: true,
      completedPerc: "PASSED",
      firstRun: true,
      failPerc: "FAIL",
      random_boolean: true,
      funtion(){
        return initialState();
      },
      firsttime: true,
      failPerc: "FAIL",
      random_boolean: true,
      boxOne:''

    };
  },
  components: {
    ProgressBar,
    BootstrapVue
  },
  methods: {
    passedFail() {
      this.random_boolean = Math.random() >= 0.5;
    },
showModal(){
  this.boxOne = ''
    this.$bvModal.msgBoxConfirm('WARNING BOX. AGREEMENT DISCLAMER CHECKBOX. Click "OK" if you agree, then click "Run Test" again to run the test')
      .then(value => {
        this.boxOne = value
      })
      .catch(err => {
        // An error occurred
      })
      if(this.boxOne){
        this.toStart()
      }
},
    toStart(event) {



  if (this.selected === undefined || this.selected.length === 0) {
    alert("please select an option");
    return 0;
  }

  var vertexNum = this.selected.includes("Vertex");
  if (vertexNum === true) {
    this.timerD = setInterval(() => {
      this.increasing_pct_Vertex = Math.min(
        this.increasing_pct_Vertex + Math.floor(Math.random() * 50 + 1),
        100
      );
      if (this.counter) {
        clearInterval(this.timerB);
      }
    }, 2000);
  }

  var EcomNum = this.selected.includes("ECommerce SRT");
  if (EcomNum === true) {
    this.timerC = setInterval(() => {
      this.increasing_pct_Ecom = Math.min(
        this.increasing_pct_Ecom + Math.floor(Math.random() * 50 + 1),
        100
      );
    }, 2000);
  }

  var RealNum = this.selected.includes("Real Time Pricing (RTP)");
  if (RealNum === true) {
    this.timerB = setInterval(() => {
      this.increasing_pct_Real = Math.min(
        this.increasing_pct_Real + Math.floor(Math.random() * 50 + 1),
        100
      );
    }, 2000);
  }

  var PNAnum = this.selected.includes("Pricing and Availability (PnA)");
  if (PNAnum === true) {
    this.timerA = setInterval(() => {
      this.increasing_pct_PnA = Math.min(
        this.increasing_pct_PnA + Math.floor(Math.random() * 50 + 1),
        100
      );
    }, 2000);
  }

Object.assign(this.$data, initialState());

  // if (this.firstRun) {
  //   return (this.firstRun = false);



  // if (this.firsttime) {
  //   return (this.firsttime = false);

  // } else {
  //   location.reload();
  // }






    },
    toStop(event) {
      clearInterval(this.timerA);
      clearInterval(this.timerB);
      clearInterval(this.timerC);
      clearInterval(this.timerD);



    },
    toggleAll(checked) {
      this.selected = checked ? this.Automation.slice() : [];
    }
  },
  watch: {
    selected(newVal, oldVal) {
      if (newVal.length === 0) {
        this.allSelected = false;
      } else if (newVal.length === this.Automation.length) {
        this.allSelected = true;
      } else {
        this.allSelected = false;
      }
    }
  }
};
</script>

<style >
</style>
