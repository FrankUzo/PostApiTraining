<template>
  <div class="home">
    <div  id="logoutBtn">
      <button class="btn btn-sm btn-danger" @click="handleLogout">Logout</button>
    </div>

  <div>
    <h3>Welcome, {{firstName}} {{ lastName }}</h3>  
  </div>
      

    <table class="table table table-striped table-hover table-bordered">
      <thead>
        <tr>
          <th scope="col">S/N</th>
          <th scope="col">Name</th>
          <th scope="col">Phone Number</th>
          <th scope="col">Discription</th>
          <th scope="col">Address</th>
          <th scope="col">Rating</th>
          <th scope="col">Image</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="restaurant in restaurants" :key="restaurant.id">
            <th scope="row">{{ restaurant.id }}</th>
          <td>{{ restaurant.name }}</td>
          <td>{{ restaurant.phone_number }}</td>
          <td>{{ restaurant.description }}</td>
          <td>{{ restaurant.address }}</td>
          <td>{{ restaurant.rating }}</td>
          <td><img :src="restaurantImageLink+restaurant.restaurant_image" class="img-responsive img-thumbnail" style="height: 150px; width: 150px;" alt=""></td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>

git
export default {
  name: 'HomeView',
  components: {},
  data() {
    return {
      restaurants: [],
      firstName: localStorage.getItem('admin_fname'),
      lastName: localStorage.getItem('admin_lname'),
      restaurantImageLink: this.storageURL + 'restaurant_images/'
    }
  },
  mounted() {
    this.getRestaurants()
  },
  methods: {
    async getRestaurants() {
      let url = this.restaurantBaseURL +'api/admin/restaurant/get_all';
      console.log('url', url)

      let bearerToken = localStorage.getItem('delivery_app_token')
            console.log('our bearerToken', bearerToken)

      const options = {
          headers: {Authorization: `Bearer ${bearerToken}`}
      }

      var response = await this.axios.get(url, options);
      console.log('restaurant response', response)

      this.restaurants = response.data.data
    },



    handleLogout(){
        localStorage.clear();
        this.$router.push('/');
    }
  },
}
</script>

<style>
 tr {
  text-align: left;
 }
 thead tr {
  text-align: center;
 }
#logoutBtn {
  text-align: right;
  margin: 10px 5px;
}
</style>
