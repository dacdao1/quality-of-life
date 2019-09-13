<template>
<div id="app">
  <h1 style="text-align: center; margin-top:10px; margin-bottom: 30px">Quality of Life</h1>
  <div>
    <div>
      <b-container class="bv-example-row">
        <b-row>
          <b-col>
            <b-form-group>
              <template slot="label">

                <b-form-checkbox style v-model="allSelected" aria-describedby="flavours" aria-controls="flavours" @change="toggleAll">{{ allSelected ? 'Un-select All' : 'Select All' }}</b-form-checkbox>
              </template>
              <br>
              <b-form-checkbox-group id="flavors" v-model="selected" name="flavors" class="ml-5" aria-label="Individual flavours" stacked>
                <b-form-checkbox v-for="Automations in Automation" v-bind:key="Automations.id" :value="Automations" class="mb-5">{{ Automations }}</b-form-checkbox>
                <label style="margin-left:-30px" class="mt-1"><input type="checkbox" v-model="checked" />I agree that I have read the following warning </label>
              </b-form-checkbox-group>
            </b-form-group>

          </b-col>

          <b-col>
            <div style="margin-top: 55px">
              <progress-bar style="margin-bottom: 35px; width: 95%" size="large" text-position="inside" :val="increasing_pct_Ecom" :text="increasing_pct_Ecom == 100 ? completedEcom: increasing_pct_Ecom + '%' "
                :bar-color="increasing_pct_Ecom == 100 ? ecomColors : normalColors " />

              <progress-bar style="margin-bottom: 35px; width: 95%" size="large" text-position="inside" :val="increasing_pct_Real" :text="increasing_pct_Real == 100 ? completedReal : increasing_pct_Real+ '%' "
                :bar-color="increasing_pct_Real == 100 ? realColors : normalColors " />

              <progress-bar style="margin-bottom: 45px; width: 95%" size="large" text-position="inside" :val="increasing_pct_PnA" :text="increasing_pct_PnA == 100 ? completedPnA : increasing_pct_PnA+ '%' "
                :bar-color="increasing_pct_PnA == 100 ? pnaColors : normalColors " />

              <progress-bar size="large" style="width: 95%" text-position="inside" :val="increasing_pct_Vertex" :text="increasing_pct_Vertex == 100 ? completedVert : increasing_pct_Vertex+ '%' "
                :bar-color="increasing_pct_Vertex == 100 ? vertColors : normalColors " />
            </div>
          </b-col>
        </b-row>
      </b-container>
    </div>

  </div>

  <b-button v-if="this.selected === undefined || this.selected.length === 0" variant="primary" style="margin-left: 90px; width:40%" size="lg" @click="nonSelect()" :disabled="!checked">Run Test</b-button>

  <b-button v-else variant="primary" style="margin-left: 90px; width:40%" size="lg" :disabled="!checked" @click="boxOne===true?toStart():showModal()">Run Test</b-button>

  <b-button variant="danger" style="margin-left: 60px; width: 40%" size="lg" v-on:click="toStop()">Stop Test</b-button>
</div>
</template>

<script>
// increasing_pct_Ecom == 100 ? barColors : normalColors
import ProgressBar from "vue-simple-progress";
import {
  setInterval
} from "timers";
import {
  clearInterval
} from "timers";
import BootstrapVue from "bootstrap-vue";

function initialState() {
  return {
    increasing_pct_Ecom: 0,
    increasing_pct_Real: 0,
    increasing_pct_PnA: 0,
    increasing_pct_Vertex: 0,
    boxOne: "",
    checked: false,
    selected: [],
    notRanVert: true,
    notRanEcom: true,
    notRanPnA: true,
    notRanReal: true,
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
      increasing_pct_Ecom: 0,
      increasing_pct_Real: 0,
      increasing_pct_PnA: 0,
      increasing_pct_Vertex: 0,
      completedVert: " ",
      completedEcom: " ",
      completedReal: ' ',
      completedPnA: ' ',
      firstRun: true,
      boxOne: "",
      checked: false,
      vertColors: ' ',
      ecomColors: ' ',
      realColors: ' ',
      pnaColors: ' ',
      notRan: true,
      normalColors: "#2196f3",
      funtion() {
        return initialState();
      }
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
    showModal() {
      this.boxOne = "";
      this.$bvModal
        .msgBoxConfirm(
          'WARNING Message: WARNING: Please be advised that by running this, you are effectively placing an order in Production.'
        )
        .then(value => {
          this.boxOne = value;
          if (this.boxOne === true) {
            this.toStart()
          }
        })
        .catch(err => {
          // An error occurred
        });
    },
    nonSelect(event) {
      if (this.selected === undefined || this.selected.length === 0) {
        alert("please select an option");
        return 0;
      }
    },
    toStart(event) {

      // this is the function for Vertex
      if (this.notRan) {
        var vertexNum = this.selected.includes("Vertex");
        var randomVert = Boolean(Math.round(Math.random()));
        if (vertexNum === true && randomVert === true) {
          this.timerD = setInterval(() => {
            this.increasing_pct_Vertex = Math.min(
              this.increasing_pct_Vertex + Math.floor(Math.random() * 50 + 1),
              100
            );
          }, 2000);
          this.completedVert = "PASSED";
          this.vertColors = "#008000";
          this.notRan = false;
        } else if (vertexNum === false) {
          clearInterval(this.timerD)
        } else {
          this.timerD = setInterval(() => {
            this.increasing_pct_Vertex = Math.min(
              this.increasing_pct_Vertex + Math.floor(Math.random() * 50 + 1),
              100
            );
          }, 2000);
          this.completedVert = "FAIL";
          this.vertColors = "#FF0000";
          this.notRan = false;
        }
      }


      // this is the function for ecom button
      if (this.notRanEcom) {
        var EcomNum = this.selected.includes("ECommerce SRT");
        var randomEcom = Boolean(Math.round(Math.random()));
        if (EcomNum === true && randomEcom === true) {
          this.timerC = setInterval(() => {
            this.increasing_pct_Ecom = Math.min(
              this.increasing_pct_Ecom + Math.floor(Math.random() * 50 + 1),
              100
            );
          }, 2000);
          this.completedEcom = "PASSED"
          this.ecomColors = "#008000"
          this.notRanEcom = false
        } else if (EcomNum === false) {
          clearInterval(this.timerC)
        } else {
          this.timerC = setInterval(() => {
            this.increasing_pct_Ecom = Math.min(
              this.increasing_pct_Ecom + Math.floor(Math.random() * 50 + 1),
              100
            );
          }, 2000);
          this.completedEcom = "FAIL"
          this.ecomColors = "#FF0000"
          this.notRanEcom = false
        }
      }

      //this is the function for the real time pricting button.
      if (this.notRanReal) {
        var RealNum = this.selected.includes("Real Time Pricing (RTP)");
        var randomReal = Boolean(Math.round(Math.random()));
        if (RealNum === true && randomReal === true) {
          this.timerB = setInterval(() => {
            this.increasing_pct_Real = Math.min(
              this.increasing_pct_Real + Math.floor(Math.random() * 50 + 1),
              100
            );
          }, 2000);
          this.completedReal = "PASSED";
          this.realColors = "#008000";
          this.notRanReal = false;
        } else if (RealNum === false) {
          clearInterval(this.timerB)
        } else {
          this.timerB = setInterval(() => {
            this.increasing_pct_Real = Math.min(
              this.increasing_pct_Real + Math.floor(Math.random() * 50 + 1),
              100
            );
          }, 2000);
          this.completedReal = "FAIL"
          this.realColors = "#FF0000"
          this.notRanReal = false
        }
      }

      // this is the button for PnA
      if (this.notRanPnA) {
        var PNAnum = this.selected.includes("Pricing and Availability (PnA)");
        var randomPnA = Boolean(Math.round(Math.random()));
        if (PNAnum === true && randomPnA === true) {
          this.timerA = setInterval(() => {
            this.increasing_pct_PnA = Math.min(
              this.increasing_pct_PnA + Math.floor(Math.random() * 50 + 1),
              100
            );
          }, 2000);
          this.completedPnA = "PASSED";
          this.pnaColors = "#008000";
          this.notRanPnA = false;
        } else if (PNAnum === false) {
          clearInterval(this.timerA)
        } else {
          this.timerA = setInterval(() => {
            this.increasing_pct_PnA = Math.min(
              this.increasing_pct_PnA + Math.floor(Math.random() * 50 + 1),
              100
            );
          }, 2000);
          this.completedPnA = "FAIL"
          this.pnaColors = "#FF0000"
          this.notRanPnA = false
        }
      }

    },
    toStop(event) {
      clearInterval(this.timerA);
      clearInterval(this.timerB);
      clearInterval(this.timerC);
      clearInterval(this.timerD);
      Object.assign(this.$data, initialState());
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
