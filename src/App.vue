<template>
  <div  id="app" :class="typeof weather.main != 'undefined' && weather.main.temp > 16 ? 'warm' : ''"> 
      <main>
        <!-- Input and search button-->
            <div class="form-row justify-content-center">
              <div class="form-group col-md-3">
                  <div class="input-group mx-auto mb-3">
                      <input type="text" 
                        placeholder="Check place.."
                        v-model="query"
                        @keypress="fetchWeather"
                        @input="fetchWeather" class="pr-2 form-control"  style="text-align:center"
                      />
                      <div class="input-group-append">
                          <button  @click="search ()" type="button" class="btn btn-outline-primary">Search</button>
                      </div>
                  </div>
              </div>
            </div>
        <!--/Input and search button-->
        <!--weather wrapper-->
          <div class="weather-wrap" v-if="typeof weather.main != 'undefined'">
            <div class="location-box">
              <div class="location">{{weather.name }}, {{weather.sys.country }} </div>
              <div class="date">{{dateBuilder()}}</div>
            </div>

            <div class="weather-box">
              <div class="temp">{{ Math.round(weather.main.temp) }}°</div>
              <div class="weather">{{ weather.weather[0].main }}</div>
            </div>
          </div>
        <!--/weather wrapper-->
        <!-- Booking section-->
        <section class="booking" v-show="isSubmitted">
          <div class="container card col-md-8 mb-6 shadow-lg">
            
              <h3 class="text-center mt-4">Book Trip to {{weather.name }}</h3>
            <form id="app2" action="">
              <h6 class="error text-center" v-for="error in errors" :key="error">{{ error }}</h6><br>
              <section v-if="step == 1" class="container col-md-8 mb-6">
                <h5 class="text-center">Step 1/3</h5>
                <div class="row">
                    <div class="col-md-6 mb-3">
                      <input required v-model="form.fname" type="text" class="form-control" placeholder="First name">
                    </div>
                    <div class="col-md-6 mb-3">
                      <input required v-model="form.lname" type="text" class="form-control" placeholder="Last name">
                    </div>
                    <div class="col-md-6 mb-3">
                      <input required v-model="form.phone" type="tel" class="form-control" placeholder="Phone number">
                    </div>
                    <div class="col-md-6 mb-3">
                      <input required v-model="form.email" type="email" class="form-control" placeholder="Email">
                    </div>
                </div>
              </section>

              <section v-if="step == 2" class="container col-md-8 mb-6">
                <h5>Step 2/3</h5>
                <div class="row">
                    <div class="col-md-6 mb-3">
                      <input required v-model="form.idnum" type="number" class="form-control" placeholder="National ID">
                    </div>
                    <div class="col-md-6 mb-3">
                      <input required v-model="form.kra" type="text" class="form-control" placeholder="KRA Pin">
                    </div>
                </div>
              </section>
          <section class="details">

          </section>
              <section v-if="step == 3" class="container col-md-8 mb-6">
                <h5>Step 3/3</h5>
                <div class="row">
                    <div class="col-md-6 mb-3">
                      <input required v-model="form.cname" type="text" class="form-control" placeholder="Company name">
                    </div>
                    <div class="col-md-6 mb-3">
                      <input required v-model="form.cloci" type="text" class="form-control" placeholder="Company location">
                    </div>
                    <div class="col-md-12 mb-3">
                      <input required v-model="form.crev" type="number" class="form-control" placeholder="Company revenue">
                    </div>
                </div>
              </section>
              
              <button 
              v-if="step !=1"
               class="btn btn-outline-primary float-left pb-2 mb-4 mt-3" @click.prevent="prevstep ()">Prev step</button>
              
              <button 
              v-if="step !=totalsteps"
               class="btn btn-outline-primary float-right pb-2 mb-4 mt-3"  @click.prevent="nextstep ()">Next step</button>
              <button 
              v-if="step ==3"
               class="btn btn-outline-primary float-right pb-2 mb-4 mt-3" @click.prevent="isSubmitted = !isSubmitted">Submit</button>
            
            </form>
          </div>
        </section>
        <section class="container  col-md-8 mb-6 "  v-show="!isSubmitted">
          <div class="card">
             <h5  class="text-center mt-4">Confirm your trip to: {{weather.name }}</h5><hr>
             <div class="row">
                    <div class="col-md-6 mb-3">
                         <h6 class="pl-2 text-center text-secondary">Name:</h6> <h5 class="pl-2 text-center">{{form.fname}} {{form.lname}}</h5>
                    </div>
                    <div class="col-md-6 mb-3">
                        <h6 class="pl-2 text-center text-secondary">Phone number:</h6> <h5 class="pl-2 text-center">{{form.phone}}</h5>
                    </div>
             </div>
             <div class="row">
                <div class="col-md-6 mb-3">
                   <h6 class="pl-2 text-center text-secondary">Email:</h6> <h5 class="pl-2 text-center">{{form.email}}</h5>
                </div>
                <div class="col-md-6 mb-3">
                    <h6 class="pl-2 text-center text-secondary">Id number:</h6> <h5 class="pl-2 text-center">{{form.idnum}}</h5>
                </div>
             </div>
             <div class="row">
                <div class="col-md-6 mb-3">
                   <h6 class="pl-2 text-center text-secondary">KRA Pin number:</h6> <h5 class="pl-2 text-center">{{form.kra}}</h5>
                </div>
                <div class="col-md-6 mb-3">
                    <h6 class="pl-2 text-center text-secondary">Company name:</h6> <h5 class="pl-2 text-center">{{form.cname}}</h5>
                </div>
             </div>
             <div class="row">
                <div class="col-md-6 mb-3">
                   <h6 class="pl-2 text-center text-secondary">Company location:</h6> <h5 class="pl-2 text-center">{{form.cloci}}</h5>
                </div>
                <div class="col-md-6 mb-3">
                    <h6 class="pl-2 text-center text-secondary">Company Revenue:</h6> <h5 class="pl-2 text-center">{{form.crev}}</h5>
                </div>
                <div class="col-md-12 mb-3 text-center">
                      <button 
                        class="btn btn-outline-success pb-2 mb-4 mt-3" @click="sendEnquiry">Confirm this booking
                      </button>
                </div>
             </div>
          </div>
        </section>
      </main>
  </div>
</template>

<script>
export default {
  name: 'App',
  data () {
    return {
    api_key: '78fb420ec0e49a11260afbd4a8bcd113',
    url_base: 'https://api.openweathermap.org/data/2.5/',
    query: '',
     errors: [],
     step: 1,
     totalsteps: 3,
     isSubmitted:true,
      form: {
        fname:null,
        lname:null,
        phone:null,
        email:null,
        idnum:null,
        kra:null,
        cname:null,
        cloci:null,
        crev:null       
      },
    isTyping: false,
    weather: {},
    }
  },
  methods: {
    fetchWeather (e) {
      if (e.key == "Enter"){
  fetch(`${this.url_base}weather?q=${this.query}&units=metric&APPID=${this.api_key}`)        .then(res => {
          return res.json();
        }).then(this.setResults);
      }
    },
    setResults (results) {
      this.weather = results;
    },
    dateBuilder () {
      let d = new Date();
      let months = ["January", "February", "March", "April", "May", "June", "July", "August", "September", "October", "November", "December"];
      let days = ["Sunday", "Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday"];
      let day = days[d.getDay()];
      let date = d.getDate();
      let month = months[d.getMonth()];
      let year = d.getFullYear();
      return `${day} ${date} ${month} ${year}`;
    },
    search () {
     fetch(`${this.url_base}weather?q=${this.query}&units=metric&APPID=${this.api_key}`)        .then(res => {
          return res.json();
        }).then(this.seTresults);
  },
  seTresults (results) {
      this.weather = results;
    },
  nextstep: function (){

    if(this.step == 1)
      {
        if(!this.form.fname){
          this.errors.push('Please fill out your first name');
          return false;

        }
        if(!this.form.lname){
          this.errors.push('Please fill out your last name');
          return false;
        }
        if(!this.form.phone){
          this.errors.push('Please fill out your phone number');
          return false;
        }
        if(!this.form.email){
          this.errors.push('Please fill out your email');
          return false;
        }
       
      }
      if(this.step == 2)
      {
        if(!this.form.idnum){
          this.errors.push('Please fill out your ID number');
          return false;
        }
        if(!this.form.kra){
          this.errors.push('Please fill out your KRA pin');
          return false;
        }
      }
     
    this.step++;
     
},
prevstep (){
  this.step--;
},
sendEnquiry (){
  if(this.step == 3)
      {
        if(!this.form.cname){
          this.errors.push('Please fill out your Company name');
          return false;
        }
        if(!this.form.cloci){
          this.errors.push('Please fill out your Company location');
          return false;
        }
        if(!this.form.crev){
          this.errors.push('Please fill out your Company Revenue');
          return false;
        }
        this.errors = null;
      }
   window.location.reload();
  this.$alert("Your information Has been sent successfully :) Thankyou for booking with us");
},
  } }

</script>

<style>
* {
  margin:0;
  padding:0;
  box-sizing: border-box;
}

body {
  font-family: 'Roboto', sans-serif;
}
.error{
  color:red;
}
#app {
  background-image: url('./assets/cold1-bg.jpg');
  background-size: cover;
  background-position: bottom;
  transition: 0.4s;
}
#app.warm {
  background-image: url('./assets/warm4-bg.jpg');
  
}

main{
  min-height: 100vh;
  padding: 25px;

  background-image: linear-gradient(to bottom, rgba(0, 0, 0, 0.25), rgba(0, 0, 0, 0.75));
}
.location-box .location{
  color:#fff;
  font-size: 32px;
  font-weight: 500;
  text-align:center;
  text-shadow: 1px 3px rgba(0, 0, 0, 0.25);
}
.location-box .date{
  color:#fff;
  font-size: 20px;
  font-weight: 300;
  font-style: italic;
  text-align:center;
  text-shadow: 1px 3px rgba(0, 0, 0, 0.25);
}
.weather-box{
  text-align:center;
}
.weather-box .temp{
  display: inline-block;
  padding:10px 25px;
  color: #fff;
  font-size: 102px;
  font-weight: 900;

  text-shadow: 3px 6px rgba(0, 0, 0, 0.25);
  background-color: rgba(255, 255, 255, 0.25);
  border-radius: 16px;
  margin:30px 0px;

  box-shadow:3px 6px rgba(0, 0, 0, 0.25)
}
.weather-box .weather{
  color:#fff;
  font-size: 48px;
  font-weight: 700;
  font-style: italic;
  text-shadow: 3px 6px rgba(0, 0, 0, 0.25);
}
</style>
