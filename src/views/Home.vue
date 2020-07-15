<template>
  <div>
    <h1>{{ pagename }}</h1>
    <button v-on:click.once="scrap">click</button>
    <p> 주제 </p>
    <button v-on:click="show_middles(big)" v-for="big in bigThemes" v-bind:key="big"> {{ big["title"] }} </button>
    <br><br>
    <button v-on:click="show_stocks(middle)" v-for="middle in middleThemes" v-bind:key="middle"> {{ middle["title"] }} </button>
    <br><br> <p> 테마주 </p>
    <button v-for="stock in stocks" v-bind:key="stock"> {{ stock["title"] }} </button>
  </div>
</template>

<script>
import axios from 'axios'
export default {
  data () {
    return {
      pagename: 'home page',
      bigThemes: [],
      middleThemes: [],
      stocks: []
    }
  },
  methods: {
    scrap: function(){
      let url = 'http://127.0.0.1:8000/main/bigs/?format=json'
      axios.get(url)
      .then(result =>{
        console.log(result.data[0]["title"]);
        for(let i = 0; i < Object.keys(result.data).length; i++){
           this.bigThemes.push(result.data[i])
        }
      })
    },
    show_middles: function(big){
      this.middleThemes = []
      let url = 'http://127.0.0.1:8000/main/bigs/' + big["id"] + '/middles/?format=json'
      axios.get(url)
      .then(result =>{
        for(let i = 0; i < Object.keys(result.data).length; i++){
           this.middleThemes.push(result.data[i])
        }
      })
    },
    show_stocks: function(middle){
      this.stocks = []
      let url = 'http://127.0.0.1:8000/main/bigs/' + middle["big_theme"] + '/middles/' + middle["id"] + '/stocks/?format=json'
      axios.get(url)
      .then(result =>{
        console.log(result.data[0])
        for(let i = 0; i < Object.keys(result.data).length; i++){
           this.stocks.push(result.data[i])
        }
      })
    }

  }
};
</script>