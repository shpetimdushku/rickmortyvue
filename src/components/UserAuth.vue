<template>
  <div>
    <nav class="navbar navbar-light bg-light py-3 mb-0" v-if="isLoggedIn">
      <div class="container-fluid justify-content-end">
        <a href="#" @click="logout" class="navbar-brand">
          <i class="fas fa-sign-out-alt"></i> Logout
        </a>
      </div>
    </nav>

    <section class="container main">
      <div v-if="!isLoggedIn">
        <!-- Register Form -->
        <div id="register" class="card" v-if="showRegisterForm">
          <div class="card-body">
            <h3 class="card-title text-center mt-3 mb-5">Sign Up</h3>
            <form @submit.prevent="register" class="row">
              <div class="row">
                <div class="col-md-6">
                  <div class="form-floating mb-3">
                    <input type="text" class="form-control rounded-3" v-model="user.firstname" placeholder="Enter first name">
                    <label for="firstname">First Name</label>
                    <div v-if="errors.firstname" class="text-danger">{{ errors.firstname }}</div>
                  </div>
                </div>
                <div class="col-md-6">
                  <div class="form-floating mb-3">
                    <input type="text" class="form-control rounded-3" v-model="user.lastname" placeholder="Enter last name">
                    <label for="lastname">Last Name</label>
                    <div v-if="errors.lastname" class="text-danger">{{ errors.lastname }}</div>
                  </div>
                </div>
                <div class="col-md-6">
                  <div class="form-floating mb-3">
                    <input type="text" class="form-control rounded-3" v-model="user.username" placeholder="Enter username">
                    <label for="username">Username</label>
                    <div v-if="errors.username" class="text-danger">{{ errors.username }}</div>
                  </div>
                </div>
                <div class="col-md-6">
                  <div class="form-floating mb-3">
                    <input type="email" class="form-control rounded-3" v-model="user.email" placeholder="Enter email">
                    <label for="email">Email</label>
                    <div v-if="errors.email" class="text-danger">{{ errors.email }}</div>
                  </div>
                </div>
                <div class="col-md-12">
                  <div class="form-floating mb-3">
                    <input type="password" class="form-control rounded-3" v-model="user.password" placeholder="Enter password">
                    <label for="password">Password</label>
                    <div v-if="errors.password" class="text-danger">{{ errors.password }}</div>
                  </div>
                </div>
                <div class="col-md-6 my-3 mx-auto">
                  <button type="submit" class="w-100 mb-2 btn btn-lg rounded-3 btn-primary">Register</button>
                </div>
                <div class="col-md-6 my-3 mx-auto">
                  <a @click="toggleForm" class="w-100 py-2 mb-2 btn btn-lg btn-outline-primary rounded-3">Login</a>
                </div>
              </div>
            </form>
          </div>
        </div>

        <!-- Login Form -->
        <div id="login" class="card" v-if="showLoginForm">
          <div class="card-body">
            <div v-if="loginError" class="alert alert-danger alert-dismissible fade show mb-5" role="alert">
              <p class="text-danger my-0 py-1">Your credentials are wrong!</p>
            </div>
            <h3 class="card-title text-center mt-3 mb-5">Login</h3>
            <form @submit.prevent="login" class="row">
              <div class="col-md-12">
                <div class="form-floating mb-3">
                  <input type="text" class="form-control" v-model="loginUsername" placeholder="Enter username">
                  <label for="loginUsername">Username</label>
                </div>
              </div>
              <div class="col-md-12">
                <div class="form-floating mb-3">
                  <input type="password" class="form-control" v-model="loginPassword" placeholder="Enter password">
                  <label for="loginPassword">Password</label>
                </div>
              </div>
              <div class="col-md-6 my-3 mx-auto">
                <button type="submit" class="w-100 mb-2 btn btn-lg rounded-3 btn-primary">Login</button>
              </div>
              <div class="col-md-6 my-3 mx-auto">
                <a @click="toggleForm" class="w-100 py-2 mb-2 btn btn-lg btn-outline-primary rounded-3">
                  Register
                </a>
              </div>
            </form>
          </div>
        </div>
      </div>

      <div v-if="isLoggedIn">
        <CharacterList />
      </div>
    </section>
  </div>
</template>

<script>
import CharacterList from './CharacterList.vue';

export default {
  name: 'UserAuth',
  components: {
    CharacterList
  },
  data() {
    return {
      user: {
        firstname: '',
        lastname: '',
        username: '',
        email: '',
        password: ''
      },
      loginUsername: '',
      loginPassword: '',
      isLoggedIn: localStorage.getItem('isLoggedIn') === 'true',
      loginError: false,
      showLoginForm: false,
      showRegisterForm: true,
      loginAttempts: 0,
      errors: {}
    };
  },
  methods: {
    toggleForm() {
      this.showLoginForm = !this.showLoginForm;
      this.showRegisterForm = !this.showRegisterForm;
    },
    validateRegistration() {
      this.errors = {};
      const { firstname, lastname, username, email, password } = this.user;
      if (!firstname) this.errors.firstname = 'First name is required';
      if (!lastname) this.errors.lastname = 'Last name is required';
      if (!username) this.errors.username = 'Username is required';
      if (!email || !this.isValidEmail(email)) this.errors.email = 'Valid email is required';
      if (!password) this.errors.password = 'Password is required';
      return Object.keys(this.errors).length === 0;
    },
    isValidEmail(email) {
      const re = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
      return re.test(email);
    },
    register() {
      if (this.validateRegistration()) {
        localStorage.setItem('user', JSON.stringify(this.user));
        this.showRegisterForm = false;
        this.showLoginForm = true;
        this.user = { firstname: '', lastname: '', username: '', email: '', password: '' }; // Clear registration form
      }
    },
    login() {
      const storedUser = JSON.parse(localStorage.getItem('user'));
      if (storedUser && storedUser.username === this.loginUsername && storedUser.password === this.loginPassword) {
        localStorage.setItem('isLoggedIn', 'true');
        this.isLoggedIn = true;
        this.loginError = false;
        this.loginUsername = ''; // Clear username field
        this.loginPassword = ''; // Clear password field
        this.loginAttempts = 0; // Reset login attempts
      } else {
        this.loginAttempts++;
        this.loginError = true;
        this.loginPassword = ''; // Clear password field
        if (this.loginAttempts >= 3) {
          // Alert after third attempt
          setTimeout(() => {
            alert('Your Credentials are wrong');
          }, 100);
        }
      }
    },
    logout() {
      localStorage.removeItem('isLoggedIn');
      this.isLoggedIn = false;
      this.showLoginForm = false;
      this.showRegisterForm = true;
    }
  }
};
</script>

