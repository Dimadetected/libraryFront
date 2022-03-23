<template>
  <div class="hello">
    <nav class="navbar navbar-expand-lg navbar-light bg-light mb-5 px-4">
      <a class="navbar-brand " href="#">Библиотека</a>
      <button class="navbar-toggler" type="button" data-toggle="collapse" data-target="#navbarNav" aria-controls="navbarNav" aria-expanded="false" aria-label="Toggle navigation">
        <span class="navbar-toggler-icon"></span>
      </button>
      <div class="col-12">
        <div class="row">
          <div class="col-6">
            <div class="collapse navbar-collapse" id="navbarNav">
              <ul class="navbar-nav">
                <li class="nav-item active">
                  <a class="nav-link" href="#" v-bind:class="{ 'b text-primary':selectedMenu('books')}" @click="updateMenu('books')">Книги</a>
                </li>
                <li class="nav-item active">
                  <a class="nav-link" href="#" v-bind:class="{ 'b text-primary': selectedMenu('authors')}" @click="updateMenu('authors')">Авторы</a>
                </li>
                <li class="nav-item active">
                  <a class="nav-link" href="#" v-bind:class="{ 'b text-primary': selectedMenu('tags')}" @click="updateMenu('tags')">Категории</a>
                </li>
                <li class="nav-item active">
                  <a class="nav-link" href="#" v-bind:class="{ 'b text-primary': selectedMenu('favorites')}" @click="updateMenu('favorites')">Избранное</a>
                </li>
              </ul>
            </div>
          </div>
          <div class="col-4" style="text-align: right">
            <div class="btn btn-danger" @click="userExit">Выход</div>
          </div>
        </div>
      </div>
    </nav>
    <div class="container">
      <div v-if="menuClick === 'books'">
        <Books :user_id="user_id"></Books>
      </div>
      <div v-else-if="menuClick === 'authors'">
        <Authors></Authors>
      </div>
      <div v-else-if="menuClick === 'tags'">
        <Tags></Tags>
      </div>
      <div v-else-if="menuClick === 'favorites'">
        <Favorites :user_id="user_id"></Favorites>
      </div>
    </div>
  </div>
</template>

<style>
 .b{
   font-weight: bold;
   font-size: 15px;
 }
</style>

<script>
import Books from './Books.vue'
import Authors from './Authors.vue'
import Tags from './Tags.vue'
import Favorites from './Favorites.vue'

export default {
  name: 'HelloWorld',
  components: {
    Books,
    Authors,
    Tags,
    Favorites,
  },
  props: {
    msg: String,
    user_id: {
      type: Number,
      required: true,
    }
  },
  data(){
    return {
      menuClick: "books"
    }
  },
  methods:{
    userExit(){
      localStorage.removeItem("user_id")
      location.reload()
    },
    selectedMenu(name){
      return this.menuClick === name;
    },
    updateMenu(part){
      this.menuClick = part
    }
  }

}
</script>