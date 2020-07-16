<template>
  <div class="admin">
    <h1>This is an admin page</h1>
    <button v-on:click="type = 'big'; title = big = '';"> 대주제 생성자 </button>
    <button v-on:click="type = 'middle'; title = big = '';" > 테마 생성자 </button>
    <button v-on:click="type = 'stock'; title = big = '';"> 관련주 생성자 </button>
    <br> <br>
    <div v-if="type === 'big'">
      <form id="big_form" @submit.prevent="big_post">
        <p> 대주제 이름: </p>
        <input required type="text" name="title" v-model="title">
        <br><br>
        <button> Submit </button>
      </form>
    </div>

    <div v-else-if="type === 'middle'">
      <form id="middle_form" @submit.prevent="middle_post">
        <p> 테마 이름 </p>
        <input required type="text" name="title" v-model="title">
        <p> 대주제 선택 </p>
        <button type="button" v-on:click="scrap"> 대주제 보기 </button>
        <br><br>
        <button type="button" v-for="big_ in bigs" v-bind:key="big_" v-on:click="big = big_['title']"> {{ big_["title"] }} </button>
        <br><br>
        <input required type="text" name="big" v-model="big">
        <br><br>
        <button > Submit </button>
      </form>
    </div> 

    <div v-else-if="type === 'stock'">
      <form id="stock_form" @submit.prevent="stock_post">
        <p> 관련주 이름 </p>
        <input required type="text" name="title" v-model="title">
        <p> 테마 선택 (복수 선택 가능) </p>
        <button type="button" v-on:click="scrap"> 주제 보기 </button>
        <br><br>
        <button v-on:click="show_middles(big_)" type="button" v-for="big_ in bigs" v-bind:key="big_"> {{ big_["title"] }} </button>
        <br><br>
        <button v-on:click="add_middles(middle)" type="button" v-for="middle in middles" v-bind:key="middle"> {{ middle["title"] }} </button>
        <br><br>
        <textarea required type="text" name="middles_submit_title" v-model="middles_submit_title" readonly cols="40" rows="5"></textarea>
        <br><br>
        <button type="submit"> Submit </button>
      </form>
    </div> 
  </div>
</template>


<script>
import axios from 'axios'
export default {
  data(){
    return {
      type : '',
      title : '',
      big : '',
      bigs : [],
      middles : [],
      middles_submit_title : [],
      middles_submit_id : []
    }
  },
  methods : {
    scrap: function(){
      this.bigs = []
      let url = 'http://127.0.0.1:8000/main/bigs/?format=json'
      axios.get(url)
      .then(result =>{
        for(let i = 0; i < Object.keys(result.data).length; i++){
           this.bigs.push(result.data[i])
        }
      })
    },
    show_middles: function(big){
      this.middles = []
      let url = 'http://127.0.0.1:8000/main/bigs/' + big["id"] + '/middles/?format=json'
      axios.get(url)
      .then(result =>{
        for(let i = 0; i < Object.keys(result.data).length; i++){
           this.middles.push(result.data[i])
        }
      })
    },
    add_middles: function(middle){
      if(this.middles_submit_id.indexOf(middle['id']) > -1){
        let result = this.middles_submit_id.indexOf(middle['id'])
        this.middles_submit_id.splice(result, 1)
        this.middles_submit_title.splice(result, 1)
      }
      else{
        this.middles_submit_id.push(middle['id'])
        this.middles_submit_title.push(middle['title'])
      }
    },
    big_post: function(){
      let url = 'http://127.0.0.1:8000/main/bigs/'
      if(confirm("대주제 '" + this.title + "' 맞나요?")){
        axios({
          method: 'post',
          url: url,
          data: {
            title: this.title
          }
        })
        .then(res =>{
          console.log(res)
          alert("대주제 '" + this.title + "' 등록 완료했습니다.")
        })
      }
    },
    middle_post: function(){
      if(confirm("대주제 '" + this.big +"'에 테마 '" + this.title + "' 맞나요?")){
        let url = 'http://127.0.0.1:8000/main/bigs/'
        let get_url = url + '?title=' + this.big
        let post_url = url
        axios.get(get_url)
        .then(res =>{
          post_url += res.data[0]["id"]
          post_url += '/middles/'
          axios({
            method: 'post',
            url: post_url,
            data: {
              title: this.title
            }
          })
          alert("대주제 '" + this.big +"'에 테마 '" + this.title + "' 등록 완료했습니다.")
        })
      }
    },
    stock_post: function(){
      if(confirm("테마 '" + this.middles_submit_title + "'에 관련주 '" + this.title + "' 맞나요?")){
        let url = 'http://127.0.0.1:8000/main/stocks/'
        axios({
          method: "post",
          url: url,
          data: {
            title: this.title,
            middle_theme : this.middles_submit_id
          }
        })
        .then(res =>{
          console.log(res)
          alert("테마 '" + this.middles_submit_title + "'에 관련주 '" + this.title + "' 등록 완료했습니다.")
        })
      }
    }
  }
}
</script>