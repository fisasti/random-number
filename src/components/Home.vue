<template>
  <div id="container">
    <div id="header">
      &nbsp;
    </div>
    <b-container fluid>
      <b-row class="numbers-row mx-1 mx-md-2 my-4">
        <b-col class="number-box align-items-center mx-1 mx-md-2"
        v-for="number in numbers" :key="number.key" :class="number.confirmed ? 'confirmed': ''">{{ number.value }}</b-col>
      </b-row>
      <b-row class="mt-5 mx-1 mx-md-2">
        <b-col class="subtitle">Low Number</b-col>
        <b-col></b-col>
        <b-col class="subtitle">High Number</b-col>
      </b-row>
      <b-row class="mx-1 mx-md-2">
        <b-col class="p-0">
          <b-form-input v-model="min" :readOnly="busy" type="number" class="w-100 text-center font-weight-bold"></b-form-input>
        </b-col>
        <b-col></b-col>
        <b-col class="text-right p-0">
          <b-form-input v-model="max" :readOnly="busy"  type="number" class="w-100 text-center font-weight-bold"></b-form-input>
        </b-col>
      </b-row>
      <b-row>
        <b-col class="text-danger font-weight-bold" v-html="error"></b-col>
      </b-row>
      <b-row class="my-2">
        <b-col>
          <button class="w-100 generate" variant="primary" v-on:click="generate()">Generate Result</button>
        </b-col>
      </b-row>
      <b-row class="my-1">
        <b-col>
          <button class="w-100 reset" variant="secondary" v-on:click="reset()">Reset</button>
        </b-col>
      </b-row>
    </b-container>
  </div>
</template>

<script>
import Vue from 'vue'
export default {
  name: 'Home',
  data() {
    return {
      busy: false,
      error: '&nbsp;',
      min: 1,
      max: 10,
      numbers: [],
      number: -1,
      previousDigit: -1
    }
  },
  methods: {
    generate () {
      if (this.busy)
        return false
      else if (this.min > this.max) {
        this.error = 'The High Number must be greater than the Low Number'
        return false
      }
      else if (this.min < 0) {
        this.error = 'The Low Number must be at least 0'
        return false
      }
      else if (this.max >= 100000) {
        this.error = 'The number must be in the range between 0 and 99999'
        return false
      }

      this.error = '&nbsp;'
      var rand = Math.random() * (this.max - this.min)
      rand = rand % 1 >= 0.5 ? parseInt(rand) + 1 : parseInt(rand)
      rand += parseInt(this.min)
      this.number = rand
      
      console.log('Random ' + this.number)
      this.number = this.number.toString();
      for (var i = this.number.length; i < this.numbers.length;i = this.number.length)
        this.number = '0' + this.number
      
      this.busy = true
      // this.next(this.numbers.length - 1)
      this.animate()
    },
    animate () {
      var digitsPerSecond = 20
      var app = this
      var intervals = []
      
      for (var k=0;k<this.numbers.length;k++) {
        this.numbers[k].value = '?'
        this.numbers[k].confirmed = false
      }

      var callback = function (key, app) {
          console.log('i', key)
          app.numbers[key].value = app.randomDigit()
        }

      for (var i =0;i<this.numbers.length;i++) {
        console.log('genero random', i, this.numbers.length)
        intervals[i] = setInterval(callback, parseInt(1000/digitsPerSecond), i, app)
      }
      var j = this.numbers.length - 1
      var stopCount = setInterval(function() {
        clearInterval(intervals[j])
        app.numbers[j].value = app.number[j]
        app.numbers[j].confirmed = true
        console.log(j)
        if (j == 0) {
          clearInterval(stopCount)
          app.busy = false
        }
        else
          j--;
      }, 3000)
      /* var interval = setInterval(function () {
        console.log('genero random', key)
        app.numbers[key].value = app.randomDigit()
      }, 50) */

      /*setTimeout(function() {
        clearInterval(interval)
        app.numbers[key].value = app.number[key]
        if (key >= 1)
          app.next(key - 1)
        else
          app.busy = false
      }, 3000)*/
    },
    randomDigit () {
      var n = this.previousDigit
      while (n == this.previousDigit)
        n = parseInt(Math.random() * 10)
      this.previousDigit = n
      return n
    },
    reset () {
      if (!this.busy) {
        for (var i=0;i<this.numbers.length;i++) {
          this.numbers[i].value = '?'
          this.numbers[i].confirmed = false
        }
        
        this.min = 1
        this.max = 10
      }
    }
  },
  computed: {
    cols () {
      var length = this.max.toString().length
      if (length > 5)
        length = 5
      if (length == 0)
        length = 1
      console.log('cols', length)
      return length
    },
  },
  watch: {
    cols: {
      deep: true,
      handler: function(newVal) {
        console.log('watcher', newVal)
        this.numbers = Array();
        for (var i=0; i < newVal; i++) {
          //this.numbers.push({ key: i, value: '?'})
          console.log(newVal)
          Vue.set(this.numbers, i, { key: i, value: this.max.toString()[i], confirmed: false })
        }
      }
    }
  },
  mounted() {
    for (var i=0; i < this.cols; i++) {
      //this.numbers.push({ key: i, value: '?'})
      Vue.set(this.numbers, i, { key: i, value: '?', confirmed: false})
    }
  },
  props: {
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
$header-aspect-ratio: 1.66;
$header-width: 14vh;

@font-face {
  font-family: SkyFont;
  src: url('../assets/stfont.ttf');
}

h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
#container {
  font-family: SkyFont !important;
  background-color: white;
  border: 1px solid white;
  border-radius: 10px;
  margin: 0 auto;
  width: 50%;
  @media (max-width: 769.999px) { width: 98%; height: 100%; }
  @media (min-width: 770px) { width: 50%; height: 95%; }
  overflow: hidden;
}
#header {
  background-image: url('../assets/logo.png');
  margin: 0 auto;
  margin-top: 1vw;
  width: $header-width;
  height: $header-width/$header-aspect-ratio;
  background-size: cover; 
}
.align-items-center {
  display: flex; 
  align-items: center;  /*Aligns vertically center */
  justify-content: center; /*Aligns horizontally center */
}
.confirmed {
  background-color: #FF00FF !important;
}
.generate, .generate:focus, .generate:active {
  padding: 3vh;
  font-family: SkyFont !important;
  font-size: 1.2em;
  background-color: #2c88d9;
  border-radius: 10px;
  border: 1px solid white;
  color: white;
  outline: none;
}
.reset, .reset:focus, .reset:active {
  padding: 3vh;
  font-family: SkyFont !important;
  font-size: 1.2em;
  border-radius: 10px;
  background-color: #999;
  border: 1px solid white;
  color: white;
  outline: none;
}
.generate:hover {
  cursor: default;
}
.generate:focus {

}
.numbers-row {
  @media (max-width: 769.999px) { height: 20vh; }
  @media (min-width: 770px) { height: 30vh; }
}
.number-box {
  background-color: #bd34d1;
  color: white;
  line-height: 1em;
  overflow: hidden;
  @media (max-width: 769.999px) { font-size: 4em; }
  @media (min-width: 770px) { font-size: 10em; }
/*  transition: 1s; */
}
.subtitle {
  font-weight: bold;
  @media (max-width: 769.999px) { font-size: 0.8em;}
  @media (min-width: 770px) { font-size: 1em; }
}
</style>
