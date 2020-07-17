<template>
  <div class="main">
    <div class="top">
      <b-button v-on:click="scrap" v-b-modal.modal-1>관심 종목 선택</b-button>
      <br>{{ obj['title'] }}
      <b-modal id="modal-1" title="BootstrapVue" size="xl">
        <div class="parent">
          <div class="child">
            <ul>
              <li v-for="big in bigThemes" v-bind:key="big">
                <b-button v-on:click="show_middles(big)"> {{ big['title'] }} </b-button>
                <br><br>
              </li>
            </ul>
          </div>
          <div class="child">
            <ul>
              <li v-for="middle in middleThemes" v-bind:key="middle">
                <b-button v-on:click="show_stocks(middle)"> {{ middle['title'] }} </b-button>
                <br><br>
              </li>
            </ul>
          </div>
          <div class="child">
            <ul>
              <li v-for="stock in stocks" v-bind:key="stock">
                <b-button v-on:click="show_stock_info(stock)"> {{ stock['title'] }} </b-button>
                <br><br>
              </li>
            </ul>
          </div>
        </div>
        <div class="parent_bottom">
          <form>
            <input required readonly type='text' name='obj_title' v-model="obj['title']">
          </form>
        </div>
      </b-modal>
    </div>
    <div class="bottom">
      <div class="bottom_left">
        <p> left </p>
      </div>
      <div class="bottom_right">
        <p> right </p>
      </div>
    </div>
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
      stocks: [],
      stock_info: "",
      visible: false,
      obj: {}
    }
  },
  methods: {
    scrap: function(){
      this.bigThemes = []
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
      this.stocks = []
      this.obj = big
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
      this.obj = middle
      this.stocks = []
      let url = 'http://127.0.0.1:8000/main/bigs/' + middle["big_theme"] + '/middles/' + middle["id"] + '/stocks/?format=json'
      axios.get(url)
      .then(result =>{
        console.log(result.data[0])
        for(let i = 0; i < Object.keys(result.data).length; i++){
           this.stocks.push(result.data[i])
        }
      })
    },
    show_stock_info: function(stock){
      this.obj = stock
      let url = 'http://127.0.0.1:8000/main/stocks/' + stock["id"] + '/?format=json'
      axios.get(url)
      .then(result =>{
        this.stock_info = result
      })
    }
  }
};
</script>
<style>
.modal-dialog,
.modal-content {
    /* 80% of window height */
    height: 90%;
}

.modal-body {
    /* 100% = dialog height, 120px = header + footer */
    max-height: calc(100% - 120px);
}
ul {
  list-style: none;
}
.parent {
  display: flex;
}
.child {
  flex: 1;
}
.parent_bottom {
  text-align: center;
}
.top {
  border-bottom: 1px solid black;
  height: 20vh;
}
.bottom {
  display:flex;
}
.bottom_left {
  flex: 1;
  height: 70vh;
  border-right: 1px solid black;
}
.bottom_right {
  flex: 1;
}
</style>