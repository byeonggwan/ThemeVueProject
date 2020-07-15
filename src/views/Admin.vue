<template>
  <div class="admin">
    <h1>This is an admin page</h1>
    <button v-on:click="type = 'big'; title = big = '';"> 대주제 생성자 </button>
    <button v-on:click="type = 'middle'; title = big = '';"> 테마 생성자 </button>
    <button v-on:click="type = 'stock'; title = big = '';"> 관련주 생성자 </button>
    <br> <br>
    <div v-if="type === 'big'">
      <form id="big_form" @submit.prevent="big_post">
        <p> 대주제 이름: </p>
        <input type="text" name="title" v-model="title">
        <br><br>
        <button> Submit </button>
      </form>
    </div>

    <div v-else-if="type === 'middle'">
      <form id="middle_form" @submit.prevent="middle_post">
        <p> 테마 이름 </p>
        <input type="text" name="title" v-model="title">
        <p> 대주제 선택 </p>
        <input type="text" name="big" v-model="big">
        <br><br>
        <button> Submit </button>
      </form>
    </div> 

    <div v-else-if="type === 'stock'">
      <form id="stock_form" @submit.prevent="stock_post">
        <p> 관련주 이름 </p>
        <input type="text" name="title" v-model="title">
        <p> 테마 선택 (복수 선택 가능) </p>
        <input>
        <br><br>
        <button> Submit </button>
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
      big : ''
    }
  },
  methods : {
    big_post: function(){
      let url = 'http://127.0.0.1:8000/main/bigs/'
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
    },
    middle_post: function(){
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
  }
}
</script>