<template>
  <div class="container">
    <div class="row mt-5">
      <div class="col-md-12 mb-3">
        <div class="card">
          <div class="card-body">
             <div class="col-md-12">
               <h3 class="font-weight-bold">The Shoppies</h3>
               <hr>
               <h6 class="font-weight-bold">Movie Title</h6>
              <form action="" @submit.prevent="searchMovie">
                <div class="input-group">
                  <input type="text" placeholder="Search for a movie" class="form-control rounded-0" v-model="movieName">
                  <button class="btn btn-primary rounded-0 position-relative btn-search"><span v-show="!loader">Search</span><span class="loader" v-show="loader"></span></button>
                </div>
              </form>
            </div>
          </div>
        </div>
      </div>
      <div class="col-md-6">
        <div class="card">
          <div class="card-body">
            <h5 class="font-weight-bold">Searched for "{{movieName}}"</h5>
            <hr>
            <ul>
              <li v-for="(movieResult, index) in movieResults" :key="index">{{movieResult.Title}} ({{movieResult.Year}}) <button class="btn btn-info btn-sm rounded-0 float-right" :disabled="disabled == index" @click="nominate(index)">Nominate</button></li>
            </ul>
          </div>
        </div>
      </div>
      <div class="col-md-6">
        <div class="card">
          <div class="card-body">
            <h5 class="font-weight-bold">Nominations</h5>
            <hr>
            <div class="alert alert-warning" v-show="message">{{message}}</div>
            <ul>
              <li v-for="(nominate, index) in nominationList" :key="index">{{nominate.Title}} ({{nominate.Year}}) <button class="btn btn-danger btn-sm rounded-0 float-right" @click="removeNomination(index)">Remove</button></li>
            </ul>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios'
export default {
  name: 'Home',
  components: {
  },
  data(){
    return{
      movieName: '',
      movieResults: [],
      nominationList: [],
      disabled: false,
      message: '',
      loader: false
    }
  },
  methods:{
    searchMovie(){
      this.loader = true
      axios.get('http://www.omdbapi.com/?s='+this.movieName+'&apikey=b3802ae9').then(response =>{
        console.log(response)
        this.loader = false
        this.movieResults = response.data.Search
        console.log(this.movieResults)
      })
    },
    nominate(index){
      if(this.nominationList.length == 5){
        this.message = "You have Nominated 5 Movies already."
      }
      else{
        this.nominationList.unshift(this.movieResults[index])
        localStorage.setItem("nominationList", JSON.stringify(this.nominationList))
        // this.disabled = true
        this.message = ''
      }
    },
    removeNomination(index){
      this.nominationList.splice(index, 1);
      localStorage.setItem("nominationList", JSON.stringify(this.nominationList))
      this.message = ''
    },
  storedNomination() {       
      if (localStorage.getItem('nominationList')) {
        try {
          this.nominationList = JSON.parse(localStorage.getItem('nominationList'));
        } catch(e) {
          console.log(e);
        }
      }
		},
	},
	mounted() {
		this.storedNomination()
	}
}
</script>
<style>
  body{
    background: #eee !important;
  }
  .btn-search{
    width: 10%;
  }
  ul li{
    margin-bottom: 10px !important;
  }
  .loader{
    width: 25px;
    height: 25px;
    border-radius: 50%;
    border-top: 4px solid black;
    border-left: 4px solid black;
    border-right: 4px solid black;
    border-bottom:4px solid #fff;
    animation: roll 1s infinite linear;
    position: absolute;
    /* margin-top: 0px; */
    top: 15%; 
    left: 35%;
    transform: translate(50%, -50%)
  }

  @keyframes roll{
    0%{
      transform: rotate(0deg)
    }
    100%{
      transform: rotate(360deg)
    }
  }
</style>
