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
                <b-form-checkbox v-for="Automations in Automation" v-bind:key="Automations.id" :value="Automations" class="mb-5" >

                  {{ Automations.text}}<b-button v-b-tooltip.hover style="margin-left: 20px; border: ; background-color: white; border: .5px solid #4CAF50; border-radius: 50%" :title="Automations.explain">
  <img src="./assets/logo1.png"> hello
  </b-button>


                  <p class="ml-5 mt-3 mb-0" v-if= "Automations.value">{{Automations.value}} <br/>{{Automations.value1}}<br/>{{Automations.value2}}</p></b-form-checkbox>



                <label style="margin-left:-30px " class="mt-1"><input style="margin-right:10px" type="checkbox" v-model="checked" />I agree that I have read the following warning </label>
              </b-form-checkbox-group>


            </b-form-group>

          </b-col>

          <b-col>
            <div style="margin-top: 55px">
              <progress-bar style="margin-bottom: 65px; width: 95%" size="large" text-position="inside" :val="increasing_pct_Ecom" :text="increasing_pct_Ecom == 100 ? completedEcom: increasing_pct_Ecom + '%' "
                :bar-color="increasing_pct_Ecom == 100 ? ecomColors : normalColors " :text-fg-color="increasing_pct_Vertex == 100? finishText : normalText" />

                <progress-bar style="margin-bottom: 20px; width: 75%; margin-left: 50px; margin-top: -35px" size="small" :val="increasing_pct_value"
                  :bar-color="increasing_pct_value == 100 ? ecomColors : normalColors " />
                  <progress-bar style="margin-bottom: 20px; width: 75%; margin-left: 50px" size="small" :val="increasing_pct_value1"
                    :bar-color="increasing_pct_value1 == 100 ? ecomColors : normalColors "/>
                    <progress-bar style="margin-bottom: 60px; width: 75%; margin-left: 50px" size="small" :val="increasing_pct_value2"
                      :bar-color="increasing_pct_value2 == 100 ? ecomColors : normalColors "/>

              <progress-bar style="margin-bottom: 60px; width: 95%" size="large" text-position="inside" :val="increasing_pct_Real" :text="increasing_pct_Real == 100 ? completedReal : increasing_pct_Real+ '%' "
                :bar-color="increasing_pct_Real == 100 ? realColors : normalColors " :text-fg-color="increasing_pct_Vertex == 100? finishText : normalText" />

              <progress-bar style="margin-bottom: 55px; width: 95%" size="large" text-position="inside" :val="increasing_pct_PnA" :text="increasing_pct_PnA == 100 ? completedPnA : increasing_pct_PnA+ '%' "
                :bar-color="increasing_pct_PnA == 100 ? pnaColors : normalColors " :text-fg-color="increasing_pct_Vertex == 100? finishText : normalText" />

              <progress-bar size="large" style="width: 95%" text-position="inside" :val="increasing_pct_Vertex" :text="increasing_pct_Vertex == 100 ? completedVert : increasing_pct_Vertex+ '%' "
                :bar-color="increasing_pct_Vertex == 100 ? vertColors : normalColors " :text-fg-color="increasing_pct_Vertex == 100? finishText : normalText" />



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
import axios from 'axios';
import ajax from "ajax";
import $ from "jquery";
import icons from "glyphicons"
function initialState() {
  return {
    increasing_pct_Ecom: 0,
    increasing_pct_Real: 0,
    increasing_pct_PnA: 0,
    increasing_pct_Vertex: 0,
    increasing_pct_value:0,
    increasing_pct_value1:0,
    increasing_pct_value2:0,
    boxOne: "",
    checked: false,
    selected: [],
    notRanVert: true,
    notRanEcom: true,
    notRanPnA: true,
    notRanReal: true
  };
}
export default {
  name: "app",
  data() {
    return {
      Automation: [
        {text: "ECommerce SRT", value: "step 1", value1:"step 2", value2:"step 3", explain:"This will test the base functionality of the website as well as OBO functionality"},
        {text: "Real Time Pricing (RTP)", explain:"Navigate to HDS site on dev environment. “Shop by category” and navigate to the PLP page"},
        {text: "Pricing and Availability (PnA)", explain:"Navigate to HDS site on dev environment. Login using valid credentials. “Shop by category” and add a valid part to the cart. Navigate to cart using icon in top right corner. Enter P.O. Box as needed and checkout cart to initiate order submission"},
        {text: "Vertex"}
      ],
      selected: [],
      allSelected: false,
      increasing_pct_Ecom: 0,
      increasing_pct_Real: 0,
      increasing_pct_PnA: 0,
      increasing_pct_Vertex: 0,
      increasing_pct_value:0,
      increasing_pct_value1:0,
      increasing_pct_value2:0,
      completedVert: " ",
      completedEcom: " ",
      completedReal: ' ',
      completeTest:' ',
      completedPnA: ' ',
      firstRun: true,
      boxOne: "",
      checked: false,
      testColors:'',
      vertColors: ' ',
      ecomColors: ' ',
      realColors: ' ',
      pnaColors: ' ',
      notRanVert: true,
      notRanPnA: true,
      notRanReal: true,
      notRanEcom: true,
      notRanTest: true,
      normalText: "#222",
      finishText: "#FFFAF0",
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
      if (this.notRanVert) {
        var vertexNum = this.selected.some(select =>select.text === "Vertex");
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
          this.notRanVert = false;
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
          this.notRanVert = false;
        }
      }


      // this is the function for ecom button
      if (this.notRanEcom) {
        var areTrue=true;
        var areTrueTrue= true;
        var EcomNum = this.selected.some(select =>select.text === "ECommerce SRT");
        var randomEcom = Boolean(Math.round(Math.random()));
        if (EcomNum === true && randomEcom === true) {

          this.timerC = setInterval(() => {
            this.increasing_pct_value = Math.min(
              this.increasing_pct_value + Math.floor(Math.random() * 50 + 1),
              100
            );
            if(this.increasing_pct_value === 100 && areTrue===true){
                this.increasing_pct_Ecom =+33;
            }

          }, 400);



          this.timerCC = setInterval(() => {
            this.increasing_pct_value1 = Math.min(
              this.increasing_pct_value1 + Math.floor(Math.random() * 50 + 1),
              100
            );
            if(this.increasing_pct_value1 === 100 && areTrueTrue === true ){
            this.increasing_pct_Ecom =+ 66;
            areTrue=false;
            }
          }, 2000);

          this.timerCCC = setInterval(() => {
            this.increasing_pct_value2 = Math.min(
              this.increasing_pct_value2 + Math.floor(Math.random() * 50 + 1),
              100
            );
            if(this.increasing_pct_value2 === 100){
              this.increasing_pct_Ecom =+ 100;
              areTrueTrue= false
            }

          }, 4000);

          this.completedEcom = "PASSED"
          this.ecomColors = "#008000"
          this.notRanEcom = false
        } else if (EcomNum === false) {
          clearInterval(this.timerC)
        } else {
          this.timerC = setInterval(() => {
            this.increasing_pct_value = Math.min(
              this.increasing_pct_value + Math.floor(Math.random() * 50 + 1),
              100
            );
            if(this.increasing_pct_value === 100 && areTrue===true){
                this.increasing_pct_Ecom =+33;
            }

          }, 400);



          this.timerCC = setInterval(() => {
            this.increasing_pct_value1 = Math.min(
              this.increasing_pct_value1 + Math.floor(Math.random() * 50 + 1),
              100
            );
            if(this.increasing_pct_value1 === 100 && areTrueTrue === true ){
            this.increasing_pct_Ecom =+ 66;
            areTrue=false;
            }
          }, 2000);

          this.timerCCC = setInterval(() => {
            this.increasing_pct_value2 = Math.min(
              this.increasing_pct_value2 + Math.floor(Math.random() * 50 + 1),
              100
            );
            if(this.increasing_pct_value2 === 100){
              this.increasing_pct_Ecom =+ 100;
              areTrueTrue= false
            }

          }, 4000);
          this.completedEcom = "FAIL"
          this.ecomColors = "#FF0000"
          this.notRanEcom = false
        }
      }

      //this is the function for the real time pricting button.
      if (this.notRanReal) {
        var RealNum = this.selected.some(select =>select.text === "Real Time Pricing (RTP)");
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
        var PNAnum = this.selected.some(select =>select.text === "Pricing and Availability (PnA)");
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
        clearInterval(this.timerCC);
          clearInterval(this.timerCCC);
      clearInterval(this.timerD);
      clearInterval(this.timerE);
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
