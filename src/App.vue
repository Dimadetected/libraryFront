<template>
  <div id="app">
    <div v-if="user_id===0" class="hello mt-5">
      <div class="container">
        <div class="offset-md-2 col-md-8 my-5 p-4 mb-5 bg-white rounded ">
          <div class="btn" :class="authClick === 'register'?'btn-success':'btn-primary'"
               @click="authClick = 'register'; error = ''">
            Зарегистрироваться
          </div>
          <div class="btn mx-2" :class="authClick === 'auth'?'btn-success':'btn-primary'" @click="authClick = 'auth'; error = ''">
            Войти
          </div>
        </div>
        <div v-if="authClick === 'register'">
          <div class="offset-md-2 col-md-8 my-5 shadow p-4 mb-5 bg-white rounded ">
            <div class="row">
              <div class="col-12">
                <h3>Регистрация:</h3>
              </div>
              <hr>
              <div class="col-12">
                <div class="alert alert-danger" v-if="error !== ''">
                  {{ error }}
                </div>
              </div>
              <div class="col-12">
                <div class="row">
                  <div class="col-4">
                    <label>Email:</label>
                  </div>
                  <div class="col-8">
                    <div class="form-group">
                      <input type="text" class="form-control" v-model="email">
                    </div>
                  </div>
                </div>
              </div>
              <div class="col-12 my-3">
                <div class="row">
                  <div class="col-4">
                    <label>Пароль:</label>
                  </div>
                  <div class="col-8">
                    <div class="form-group">
                      <input type="password" class="form-control" v-model="password">
                    </div>
                  </div>
                </div>
              </div>
              <div class="col-12">
                <div class="btn btn-success" @click="registerUser">Зарегистрироваться</div>
              </div>
            </div>
          </div>
        </div>
        <div v-else-if="authClick === 'auth'">
          <div class="offset-md-2 col-md-8 my-5 shadow p-4 mb-5 bg-white rounded ">
            <div class="row">
              <div class="col-12">
                <h3>Авторизация:</h3>
              </div>
              <hr>
              <div class="col-12">
                <div class="alert alert-danger" v-if="error !== ''">
                  {{ error }}
                </div>
              </div>
              <div class="col-12">
                <div class="row">
                  <div class="col-4">
                    <label>Email:</label>
                  </div>
                  <div class="col-8">
                    <div class="form-group">
                      <input type="text" class="form-control" v-model="email">
                    </div>
                  </div>
                </div>
              </div>
              <div class="col-12 my-3">
                <div class="row">
                  <div class="col-4">
                    <label>Пароль:</label>
                  </div>
                  <div class="col-8">
                    <div class="form-group">
                      <input type="password" class="form-control" v-model="password">
                    </div>
                  </div>
                </div>
              </div>
              <div class="col-12">
                <div class="btn btn-success" @click="loginUser">Войти</div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
    <Main v-else :user_id="user_id" msg="Welcome to Your Vue.js App"/>
  </div>
</template>

<script>
import Main from './components/Main.vue'

export default {
  name: 'App',
  data() {
    return {
      error: "",
      email: "",
      password: "",
      authClick: "auth",
      user_id: 0
    }
  },
  mounted() {
    var newUserId = localStorage.getItem("user_id")
    if (newUserId != null) {
      this.user_id = parseInt(newUserId)
    }
  },
  methods: {
    registerUser() {
      if (confirm('Вы точно хотите зарегистрироваться?')) {
        fetch('http://localhost:8000/users/register', {
          method: "post",
          body: JSON.stringify(
              {
                email: this.email,
                password: this.password,
              }
          )
        }).then(res => res.json())
            .then(res => {
              if (res.error !== null) {
                this.error = res.error
              } else {
                localStorage.setItem('user_id', res.data)
                this.user_id = res.data
              }
            })
            .catch(res => {
              console.log(res)
              this.error = "Ошибка регистрации"
            })
      }
    },
    loginUser() {
      if (confirm('Вы точно хотите войти?')) {
        fetch('http://localhost:8000/users/login', {
          method: "post",
          body: JSON.stringify(
              {
                email: this.email,
                password: this.password,
              }
          )
        }).then(res => res.json())
            .then(res => {
              if (res.error !== null) {
                this.error = res.error
              } else {
                localStorage.setItem('user_id', res.data)
                this.user_id = res.data
              }
            })
        .catch(res => {
          console.log(res)
          this.error = "Ошибка входа"
        })
      }
    },
    selectedMenu(name) {
      return this.authClick === name;
    },
  },
  components: {
    Main,
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}
</style>
