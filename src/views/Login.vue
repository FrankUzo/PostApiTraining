<template>
  <form @submit.prevent="handleValidation">
    <h5>Welcome To Our Login Page</h5>
    <input type="email" placeholder="Enter your email here" v-model="email" required>
    <br><br>
    <input type="password" placeholder="Enter your password here" v-model="password" required>
    <div v-if="passwordError" class="error">
        {{ passwordStatus }}
    </div>
    <button>Click to Login</button>
  </form>
</template>

<script>

export default {
    data() {
        return {
            email: '',
            password: '',
            passwordError: false,
            passwordStatus: 'password should be at least 5 chars long'
        }
    },
    methods: {
        handleValidation(e) {
            e.preventDefault();
    
            console.log('handleSubmit')
            console.log('password' + this.password)
           if (this.password.length > 5) {
            this.passwordError = false
            this.passwordStatus = ''
            console.log('pushing to home' )
            this.handleLogin( this.email, this.password)
           }
           else {
            this.passwordError = true
            this.passwordStatus = 'password should be at least 5 chars long'
            alert("password should be at least 5 chars long")
           }    
            
        },
        async handleLogin (email, password) {
            let url = this.restaurantBaseURL +'api/admin/auth/login';
            let params = {email: email, password: password}
            console.log('login params' , params)
            await this.axios.post(url, params)
            .then((response) => {
                console.log('login response' , response)

            if (response.data.access_token != null) {
            localStorage.setItem('isLoggedIn', true);
            localStorage.setItem('delivery_app_token', response.data.access_token);
            localStorage.setItem('current_user_email', this.email);
            this.getAdminProfile();
          }else{
              alert('Login Failed - no access token');
          }
        })
        .catch(error => {
                alert('Login Failed - incorrect email or password')
                console.log("Error is: ");
                console.log(error);
         });

            // this.$router.push('/home')
        },

        //Authorization call
        async getAdminProfile() {
            let url = this.restaurantBaseURL +'api/admin/profile'
            let bearerToken = localStorage.getItem('delivery_app_token')
            console.log('our bearerToken', bearerToken)

            const options = {
                headers: {Authorization: `Bearer ${bearerToken}`}
            }
            await this.axios.get(url, options)
            .then((response) => {
                console.log('response', response)
                localStorage.setItem('admin_role', response.data.role);
                localStorage.setItem('admin_phone', response.data.phone_number);
                localStorage.setItem('admin_fname', response.data.first_name);
                localStorage.setItem('admin_lname', response.data.last_name);
                localStorage.setItem('admin_email', response.data.email);
                localStorage.setItem('admin_id', response.data.id);
                this.$router.push('/home');

            })
           
            .catch(error => {
                
                console.log('error', error)
                alert('Could not get admin profile-Please try again')
            })
        },
    },
}
</script>


<style>
    form {
        max-width: 400px;
        padding: 30px;
        background: wheat;
        margin: 160px auto;
        border-radius: 30px;
    }
    form h5 {
        background: rgb(46, 46, 228);
        color: white;
        padding: 10px 0;
        font-family: cursive;
        font-size: 1.5em;
        letter-spacing: 1px;
        margin-bottom: 30px;
        border-radius: 20px 20px 0 0px;
    }
    input {
        display: inline-block;
        box-sizing: border-box;
        width: 100%;
        padding: 0 10px;
    }
    form button {
        background: rgb(46, 46, 228);
        margin: 30px 0;
        width: 100%;
        padding: 2px;
        color: white;
        border: none;
        border-radius: 10px;
        cursor: pointer;
    }
    .error {
        color: red;
        font-size: 0.8rem;
    }
</style>