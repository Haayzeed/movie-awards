<template>
<div>
<div class="col-md-12 banner">
<h1 class="text-white text-uppercase font-weight-bold">The Shoppies Movie Awards</h1>
<hr>
</div>
  <div class="container mb-3">
    <div class="row mt-5 mb-5">
        <!-- Search Form -->
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
                <p class="text-danger">{{errorMessage}}</p>
              </form>
            </div>
          </div>
        </div>
      </div>
      <!-- Search Result -->
      <div class="col-md-6">
        <div class="card">
          <div class="card-body">
            <h5 class="font-weight-bold">Searched for "{{movieName}}"</h5>
            <hr>
            <ul>
              <li v-for="(movieResult, index) in movieResults" :key="index">{{movieResult.Title}} ({{movieResult.Year}}) <button :disabled="disabled" class="btn btn-info btn-sm rounded-0 float-right"  @click.once="nominate(index, $event)" >Nominate</button></li>
            </ul>
          </div>
        </div>
      </div>
      <!-- Nomination List -->
      <div class="col-md-6">
        <div class="card">
          <div class="card-body">
            <h5 class="font-weight-bold">Nominations <span class="float-right" style="font-size:16px;">{{this.nominationList.length}} Nomination of 5</span></h5>
            <hr>
            <div class="alert alert-warning" v-show="message">{{message}}</div>
            <ul v-if="nominationList">
              <li v-for="(nominate, index) in nominationList" :key="index">{{nominate.Title}} ({{nominate.Year}}) <button class="btn btn-danger btn-sm rounded-0 float-right" @click="removeNomination(index)">Remove</button></li>
            </ul>
          </div>
        </div>
      </div>
    </div>
    <p class="text-center">&copy; The Shoppies Movie Awards</p>
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
      loader: false,
      errorMessage: '',
      // nomination: 5
    }
  },
  methods:{
    // Search Movie
    searchMovie(){
      this.loader = true
      axios.get('https://www.omdbapi.com/?s='+this.movieName+'&apikey=b3802ae9').then(response =>{
        this.loader = false
        if(response.data.Response == "False"){
          this.errorMessage = 'Movie Not Found !'
        }
        else{
          this.movieResults = response.data.Search
          this.errorMessage = ''
        }
      })
    },
    // Nominate Movie and store in LocalStorage
    nominate(index, event){
      if(this.nominationList.length == 5){
        this.message = "You have Nominated 5 Movies already."
      }
      else{
        this.nominationList.unshift(this.movieResults[index])
        localStorage.setItem("nominationList", JSON.stringify(this.nominationList))
        this.message = ''
        event.target.disabled = true
        this.nomination = this.nominationList.length -1
      }
    },
    // Remove a Novie from Nomination
    removeNomination(index){
      this.nominationList.splice(index, 1);
      localStorage.setItem("nominationList", JSON.stringify(this.nominationList))
      this.message = ''
    },
    // Get Nomination from LocalStorage
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
    // Load Nomination List from LocalStorage
		this.storedNomination()
	}
}
</script>
<style>
  body{
    background: #eee !important;
  }
  .banner{
    background-image: url('../assets/movie_poster_design.jpg');
    background-color: rgba(0,0,0,0.5);
    background-blend-mode: overlay;
    height: 300px !important;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
  }
  .banner hr{
    border: 2px solid #fff;
    width: 15%;    
  }
  .btn-search{
    width: 10%;
  }
  input:focus, button:focus{
    box-shadow: none !important;
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
