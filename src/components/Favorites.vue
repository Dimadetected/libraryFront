<template>
  <div class="col-md-12">
    <div class="row my-3">
      <div class="text-right col-md-2 offset-md-10">
        <div v-if="favoritesShow !== 'all'" class="btn btn-primary" @click="favoritesShowChange('all')">Просмотр
        </div>
      </div>
    </div>
    <table class="table table-bordered" v-if="favoritesShow === 'all'">
      <thead>
      <tr>
        <td>Название</td>
        <td>Автор</td>
        <td>Прогресс чтения</td>
        <td></td>
      </tr>
      </thead>
      <tbody>
      <tr v-for="favorite in favorites" :key="favorite.id">
        <td><span class="text-primary" style="cursor: pointer"
                  @click="favoritesShowChange('bookProcessing'); SelectedProcessingID= favorite.book_id">{{ getBookName(favorite.book_id) }}</span></td>
        <td>{{ authors[books[favorite.book_id]] }}</td>
        <td>
          <div v-if="processings.has(favorite.book_id)">
            <span class="text-primary">{{ processings.get(favorite.book_id).page }}</span> / <span class="text-success">{{ processings.get(favorite.book_id).pages }}</span>
            | {{ (processings.get(favorite.book_id).page * 100 / processings.get(favorite.book_id).pages).toFixed(2) }}%
          </div>
          <div v-else>
            0 / 0 | 0%
          </div>
        </td>
        <td>
          <div class="row">
            <div class="btn btn-success col-5 offset-1 p-1" @click="processingFix(favorite)">Прогресс</div>
            <div class="btn btn-danger col-5 offset-0  p-1" @click="favoriteDelete(favorite.id)">Удалить</div>
          </div>
        </td>
      </tr>
      </tbody>
    </table>
    <div v-else-if="favoritesShow === 'create'">
      <div class="container mt-5">
        <div class="col-12">
          <div class="form-group">
            <div class="row">
              <div class="col-4">Книга:</div>
              <div class="col-8" style="text-align: left !important;"> {{ this.getBookName(processingFormBookID) }}</div>
            </div>
          </div>
        </div>
        <div class="col-12 my-3">
          <div class="form-group">
            <div class="row">
              <div class="col-4 mt-3">
                <label for="processingFormPages">Количество страниц в книге:</label>
              </div>
              <div class="col-8" v-if="processingFormID === 0">
                <input type="text" id="processingFormPages" class="form-control col-8" v-model="processingFormPages"
                       aria-label="Название">
              </div>
              <div class="col-8" style="text-align: left !important;" v-else> <b> {{ processingFormPages }}</b></div>

            </div>
          </div>
        </div>
        <div class="col-12 my-3">
          <div class="form-group">
            <div class="row">
              <div class="col-4">
                <label for="processingFormPage">Количество страниц прочтенных:</label>
              </div>
              <div class="col-8">
                <input type="text" id="processingFormPage" class="form-control col-8" v-model="processingFormPage"
                       aria-label="Название">
              </div>
            </div>
          </div>
        </div>
        <div class="col-4 btn btn-success mt-4" @click="processingSave()">Сохранить</div>
      </div>
    </div>
    <div v-else-if="favoritesShow === 'bookProcessing'">
      <div class="container mt-5">
        <table class="table table-bordered">
          <thead>
          <tr>
            <th>Прочтено</th>
            <th>Всего</th>
            <th>Дата</th>
            <th></th>
          </tr>
          </thead>
          <tbody>
          <tr v-for="(pc,index) in processingsArr" :key="pc.id" :class="getClassTable(index)">
            <td v-if="SelectedProcessingID === pc.book_id"> {{pc.page}} <span class="text-success">({{pc.page * 100 / pc.pages}}%)</span></td>
            <td v-if="SelectedProcessingID === pc.book_id"> {{pc.pages}}</td>
            <td v-if="SelectedProcessingID === pc.book_id"> {{pc.created}}</td>
            <td v-if="SelectedProcessingID === pc.book_id"> <div class="btn btn-danger" @click="deleteProcessing(pc.id)">Удалить</div> </td>
          </tr>
          </tbody>
        </table>
      </div>
    </div>
  </div>
</template>

<style>
</style>

<script>
export default {
  name: 'favoritesGet',
  props: {
    user_id: {
      type: Number,
      required: true,
    }
  },
  data() {
    return {
      SelectedProcessingID: 0,
      formID: 0,
      formName: "",
      booksArr: [],
      books: [],
      authors: [],
      favoritesShow: "all",
      favorites: [],
      processings: [],
      processingsArr: [],

      processingFormID: 0,
      processingFormUserID: 0,
      processingFormBookID: 0,
      processingFormPage: 0,
      processingFormPages: 0,
    }
  },
  mounted() {
    this.favoritesGet()
  },
  methods: {
    getClassTable(id){
      var table = ""
      if (id === 0){
        table = "table-success"
      }

      return table
    },
    async deleteProcessing(id){
      if (!confirm('Вы точно хотите удалить ?')) {
        return
      }

      var path = 'http://localhost:8000/books/processing/delete/'+ id
      await fetch(path, {
        method: "post",
      })

      await this.processingGet()
    },
    favoriteEdit(id) {
      this.favorites.map(favorite => {
        if (id === favorite.id) {
          this.formID = favorite.id
          this.formName = favorite.name
        }
      })
      this.favoritesShowChange('create')
    },

    favoritesShowChange(msg) {
      this.favoritesShow = msg
      if (msg === 'all') {
        this.formID = 0
        this.formName = 'Название'
      }
    },
    async favoritesGet() {
      await this.authorsGet()
      await this.booksGet()
      await fetch('http://localhost:8000/books/favorite/' + this.user_id)
          .then(res => res.json())
          .then(res => {
            this.favorites = res.data
          })
    },
    async processingGet() {
      await fetch('http://localhost:8000/books/processing/' + this.user_id)
          .then(res => res.json())
          .then(res => {
            this.processings = new Map()
            if (res.data.length > 0) {
              this.processingsArr = res.data
              res.data.map(p => {
                if (!this.processings.has(p.book_id)) {
                  this.processings.set(p.book_id, p)
                }
              })
            }
            console.log("proc",this.processings)

          })
    },
    getBookName(id) {
      var name = ""
      this.booksArr.map(b => {
        if (b.id === id) {
          name = b.name
        }
      })
      return name
    },
    async getAuthor(id) {
      var name1 = ""
      console.log(3, this.authors, id)
      this.booksArr.map(b => {
        if (b.id === id) {
          name1 = this.authors[b.author_id]
        }
      })
      return name1
    },
    async booksGet() {
      await fetch('http://localhost:8000/books?limit=1000000&offset=0')
          .then(res => res.json())
          .then(res => {
            this.booksArr = res.data
            this.booksArr.map(b => {
              this.books[b.id] = b.author_id
            })
          })
      await this.processingGet()
    },
    async favoriteDelete(id) {
      if (confirm('Вы точно хотите удалить из избранного?')) {
        await fetch('http://localhost:8000/books/favorite/delete/' + id, {
          method: "POST"
        })
        await this.favoritesGet()
      }
    },
    async processingFix(favorite) {
      this.processingFormID = 0
      this.processingFormPage = 0
      this.processingFormPages = 0
      this.processingFormBookID = favorite.book_id
      this.processingFormUserID = favorite.user_id

      this.favoritesShow = "create"
      console.log("test:",this.processings)
      if (this.processings.has(favorite.book_id)) {
        this.processingFormID = favorite.id
        this.processingFormBookID = this.processings.get(favorite.book_id).book_id
        this.processingFormUserID = this.processings.get(favorite.book_id).user_id
        this.processingFormPage = this.processings.get(favorite.book_id).page
        this.processingFormPages = this.processings.get(favorite.book_id).pages
      }
      // await fetch('http://localhost:8000/books/favorite/delete/' + id, {
      //   method: "POST"
      // })
      // await this.favoritesGet()
    },
    async authorsGet() {
      await fetch('http://localhost:8000/authors?limit=1000000')
          .then(res => res.json())
          .then(res => {
            res.data.map(author => {
              this.authors[author.id] = author.name
            })
          })
    },
    async processingSave() {
      if (!confirm('Вы точно хотите сохранить изменения ?')) {
        return
      }

      var path = 'http://localhost:8000/books/processing'
      await fetch(path, {
        method: "post",
        body: JSON.stringify(
            {
              book_id: parseInt(this.processingFormBookID),
              user_id: parseInt(this.processingFormUserID),
              page: parseInt(this.processingFormPage),
              pages: parseInt(this.processingFormPages),
            }
        )
      })

      await this.processingGet()

      this.favoritesShow = 'all'
    }
  }
}
</script>