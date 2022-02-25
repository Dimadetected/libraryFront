<template>
  <div class="hello">
    <nav class="navbar navbar-expand-lg navbar-light bg-light mb-5 px-4">
      <a class="navbar-brand " href="#">Библиотека</a>
      <button class="navbar-toggler" type="button" data-toggle="collapse" data-target="#navbarNav" aria-controls="navbarNav" aria-expanded="false" aria-label="Toggle navigation">
        <span class="navbar-toggler-icon"></span>
      </button>
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
        </ul>
      </div>
    </nav>
    <div class="container">
      <div v-if="menuClick === 'books'">
        <Books></Books>
      </div>
      <div v-else-if="menuClick === 'authors'">
        <Authors></Authors>
      </div>
      <div v-else-if="menuClick === 'tags'">
        <Tags></Tags>
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

export default {
  name: 'HelloWorld',
  components: {
    Books,
    Authors,
    Tags
  },
  props: {
    msg: String
  },
  data(){
    return {
      menuClick: "books"
    }
  },
  methods:{
    selectedMenu(name){
      return this.menuClick === name;
    },
    updateMenu(part){
      this.menuClick = part
    }
  }

}
</script>