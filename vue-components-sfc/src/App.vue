<script>
import EditModal from './components/EditModal.vue';
import ViewModal from './components/ViewModal.vue';
import { Trip } from './models/Trip';

export default {
  components: {EditModal, ViewModal},
  data: function(){
         let trips = [
             // trying to get Models to work
             new Trip('Denver','2024-03-25', '2024-03-30', '$', true, false),
             new Trip('Green Bay','2024-04-25', '2024-04-30', '$$', true, false),
             new Trip('Grand Canyon','2024-05-25', '2024-05-30', '$$$', true, true)
         ];
        return {
            newTrip: new Trip(),
            editItem: {},
             tripList: trips
        }
    },

    
    methods: {
        addIt: function(){
            // add item to the list
            this.tripList.push(this.newTrip);

            //clear the form
            this.newTrip = new Trip();
        },
        deleteIt(item){
            console.log(item);
            console.log(this.tripList)
            this.tripList.splice(this.tripList.indexOf(item),1);

        }
    },
    computed: {
        favoriteList: function(){
            return this.tripList.filter(function(item){
                return !item.completed && item.favorited;
            })
        },
        completedList: function(){
            return this.tripList.filter(function(item){
                return item.completed;
            })
        },
    },
    mounted: function() {
        if(localStorage.getItem('tripList')){
            this.tripList = JSON.parse(localStorage.getItem('tripList'));
        }
    },

    watch: {
        tripList: {
            handler: function(newList){
                localStorage.setItem('tripList', JSON.stringify(this.tripList));
            },
            deep: true,
        }
    }
}

</script>

<template>
  <nav class="navbar navbar-expand-lg navbar-light bg-light">
    <a class="navbar-brand ml-3"><svg xmlns="http://www.w3.org/2000/svg" height="1.25em" viewBox="0 0 512 512">
        <path
          d="M464 256A208 208 0 1 0 48 256a208 208 0 1 0 416 0zM0 256a256 256 0 1 1 512 0A256 256 0 1 1 0 256zm306.7 69.1L162.4 380.6c-19.4 7.5-38.5-11.6-31-31l55.5-144.3c3.3-8.5 9.9-15.1 18.4-18.4l144.3-55.5c19.4-7.5 38.5 11.6 31 31L325.1 306.7c-3.2 8.5-9.9 15.1-18.4 18.4zM288 256a32 32 0 1 0 -64 0 32 32 0 1 0 64 0z" />
      </svg> Trip Planner</a>
    <button id="addButton" class="btn btn-primary d-flex ms-auto order-5" data-toggle="modal"
      data-target="#newTripModal">Add Trip</button>
  </nav>

  <div>
    <div class="jumbotron jumbotron-fluid">
      <div class="jumboText container">
        <h1 class="display-4">Where to Next?</h1>
        <p class="lead">Plan your next getaway!</p>
      </div>
    </div>
  </div>

  <div class="container">
    <div class="row">
      <div class="col">
          <img src="https://www.travelandleisure.com/thmb/f3J2C3ON_T4iMzdslgFIjwGaK1E=/1500x0/filters:no_upscale():max_bytes(150000):strip_icc()/travel-couple-trip-vacation-COUPLMISTAKES0720-676da6637e304768b389378758075494.jpg" alt="blank user icon" id="userImg">
      </div>
      <div class="col">
        <h1>Welcome, User</h1>
        <button type="button" class="btn btn-primary">Edit Account</button>
      </div>
    </div>
  </div>

  <!-- New Trip Modal -->
  <div class="modal fade" id="newTripModal" tabindex="-1" role="dialog" aria-labelledby="newTripModalLabel" aria-hidden="true">
        <div class="modal-dialog" role="document">
          <div class="modal-content">
            <div class="modal-header">
              <h5 class="modal-title" id="newTripModalLabel">New Trip</h5>
              <button type="button" class="close" data-dismiss="modal" aria-label="Close">
                <span aria-hidden="true">&times;</span>
              </button>
            </div>
            <div class="modal-body">
                <form>
                    <div class="form-group">
                      <label for="nameOfTrip">Name of Trip</label>
                      <input v-model="newTrip.name" type="text" class="form-control" id="nameOfTrip" placeholder="Trip Name...">
                    </div>

                    <div class="row">
                        <div class="col">
                            <label for="start">Start date: </label>
                            <input v-model="newTrip.startDate" type="date" class="form-control" id="start" name="trip-start" min="2023-11-01" max="2100-12-31" />
                        </div>
                        <div class="col">
                            <label for="start">End date: </label>
                            <input v-model="newTrip.endDate" type="date" class="form-control" id="end" name="trip-end" min="2023-11-01" max="2100-12-31" />
                        </div>
                    </div>

                    <div class="form-group">
                        <label for="tripPrice">Trip Price</label>
                        <select v-model="newTrip.price" id="tripPrice" class="form-control">
                          <option selected> </option>
                          <option>$</option>
                          <option>$$</option>
                          <option>$$$</option>
                        </select>
                    </div>

                </form>
            </div>
            <div class="modal-footer">
              <button type="button" class="btn btn-secondary" data-dismiss="modal">Close</button>
              <button type="button" class="btn btn-primary" data-dismiss="modal" v-on:click.prevent="addIt">Add New Trip</button>
            </div>
          </div>
        </div>
    </div>

    <div class="container" id="userContainer">
    <ul class="nav nav-tabs nav-justified mb-3" id="tabs-tab" role="tablist">
      <li class="nav-item">
        <a class="nav-link active" id="tabs-my-trips-tab" data-toggle="tab" href="#tabs-my-trips" role="tab"
          aria-controls="tabs-my-trips" aria-selected="true">Completed Trips</a>
      </li>
      <li class="nav-item">
        <a class="nav-link" id="tabs-favorites-tab" data-toggle="tab" href="#tabs-favorites" role="tab"
          aria-controls="tabs-favorites" aria-selected="false">Planned Trips</a>
      </li>
    </ul>
    <div class="tab-content" id="tabs-tabContent">
      <div class="tab-pane fade show active" id="tabs-my-trips" role="tabpanel" aria-labelledby="tabs-my-trips-tab">
        <div class="card-deck">
          <div v-for="(item, index) in completedList" v-bind:key="item.name" class="card">
            <img class="card-img-top"
              src= https://static6.depositphotos.com/1160567/648/i/450/depositphotos_6482528-stock-photo-travel-destination.jpg
              alt="Card image cap">
            <div class="card-body">
                <h2 class="card-title">{{ item.name }}</h2>
                <h6 class="card-title">Start Date: {{ item.startDate }}</h6>
                <h6 class="card-title">End Date: {{ item.endDate }}</h6>
                <h6 class="card-title">Price: {{ item.price }}</h6>
              <div class="form-check">
                <input v-model="item.favorited" class="form-check-input" type="checkbox" value="" v-bind:id="'Favorite-' + index ">
                <label class="form-check-label" v-bind:for="'Favorite-' + index ">
                   {{item.name}} Trip Planned
                </label>
              </div>
              <div class="form-check">
                <input v-model="item.completed" class="form-check-input" type="checkbox" value="" v-bind:id="'MyTrip-' + index ">
                <label class="form-check-label" v-bind:for="'MyTrip-' + index ">
                  {{item.name}} Trip Completed
                </label>
              </div>
              <!-- Button trigger modal -->
              <button type="button" class="btn btn-primary" data-toggle="modal" data-target="#viewTripModal" v-on:click.prevent="editItem = item">
                View your Trip
              </button>
              <button type="button" class="btn btn-secondary m-2" v-on:click.prevent="deleteIt(item)">
                Delete your Trip
              </button>
            </div> 
          </div>
        </div>
      </div>

      <div class="tab-pane fade" id="tabs-favorites" role="tabpanel" aria-labelledby="tabs-favorites-tab">
        <div class="card-deck">
          <div v-for="(item, index) in favoriteList" v-bind:key="item.name" class="card">
            <img class="card-img-top"
            src= https://static6.depositphotos.com/1160567/648/i/450/depositphotos_6482528-stock-photo-travel-destination.jpg
              alt="Card image cap">
            <div class="card-body">
              <h2 class="card-title">{{ item.name }}</h2>
              <h6 class="card-title">Start Date: {{ item.startDate }}</h6>
              <h6 class="card-title">End Date: {{ item.endDate }}</h6>
              <h6 class="card-title">Price: {{ item.price }}</h6>
              <div class="form-check">
                <input v-model="item.favorited" class="form-check-input" type="checkbox" value="" v-bind:id="'Favorite-' + index ">
                <label class="form-check-label" v-bind:for="'Favorite-' + index ">
                  {{item.name}} Trip Planned
                </label>
              </div>
              <div class="form-check">
                <input v-model="item.completed" class="form-check-input" type="checkbox" value="" v-bind:id="'MyTrip-' + index ">
                <label class="form-check-label" v-bind:for="'MyTrip-' + index ">
                  {{item.name}} Trip Completed
                </label>
              </div>
              <!-- Button trigger modal -->
              <button type="button" class="btn btn-primary" data-toggle="modal" data-target="#editTripModal" v-on:click.prevent="editItem = item">
                Edit your Trip
              </button>
              <button type="button" class="btn btn-secondary m-2" v-on:click.prevent="deleteIt(item)">
                Delete your Trip
              </button>
            </div> 
          </div>
        </div>
      </div>
    </div> 
  </div>

  <!-- Edit Trip Modal-->
  <edit-trip-modal v-model="editItem"></edit-trip-modal>

  <!-- Edit Trip Modal-->
    <view-trip-modal v-model="editItem"></view-trip-modal>

  <div class="container">
    <footer class="py-3 my-4">
      <ul class="nav justify-content-center border-bottom pb-3 mb-3">
        <li class="nav-item">
          <a href="index.html" class="nav-link px-2 text-body-secondary">Home</a>
        </li>
        <li class="nav-item">
          <a href="TopLocations.html" class="nav-link px-2 text-body-secondary">Trending</a>
        </li>
        <li class="nav-item">
          <a href="user.html" class="nav-link px-2 text-body-secondary">Community</a>
        </li>
        <li class="nav-item">
          <a href="#" class="nav-link px-2 text-body-secondary">FAQs</a>
        </li>
      </ul>
      <p class="text-center text-body-secondary">© 2023 Company, Inc</p>
    </footer>
  </div>

</template>

<style></style>
