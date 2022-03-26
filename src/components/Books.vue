<template>
  <div class="col-md-12">
    <div class="row">
      <div class="col-md-3">Название</div>
      <div class="col-md-3">Автор</div>
      <div class="col-md-3">Год</div>
      <div class="col-md-3">Категории</div>
    </div>
    <div class="row">
      <div class="col-md-3">
        <input type="text" class="form-control "
               v-model="filterName" aria-label="Название" @change="booksGet()">
      </div>
      <div class="col-md-3">
        <select class="form-control" name="" v-model="filterAuthor" @change="booksGet()">
          <option value="0">Все</option>
          <option v-for="(name,id) in authors" :key="id" :value="id">{{name}}</option>
        </select>
      </div>
      <div class="col-md-3">
        <input type="text" class="form-control "
               v-model="filterYear" aria-label="Название" @change="booksGet()">
      </div>
      <div class="col-md-3">
        <select class="form-control" name="" v-model="filterTags">
          <option value="0">Все</option>
          <option v-for="(name,id) in tagsMap" :key="id" :value="id">{{name}}</option>
        </select>
      </div>
    </div>
    <div class="row">
      <div class="col-auto my-1" v-for="(name,id) in formTagIdsFilter" :key="id">
        <button class="btn" :class="'btn-primary'"
                @click="changeTagStatusFilter(name)">{{ tagsMap[name] }}
        </button>
      </div>
    </div>
    <div class="row my-3">
      <div class="text-right col-md-2 offset-md-10">
        <div v-if="booksShow === 'all'" class="btn btn-success" @click="booksShowChange('create')">Добавить</div>
        <div v-if="booksShow !== 'all'" class="btn btn-primary" @click="booksShowChange('all')">Просмотр</div>
      </div>
    </div>
    <div class="row" v-if="booksShow === 'all'">
      <div class="offset-md-3 col-md-6 my-5 shadow p-4 mb-5 bg-white rounded " v-for="book in books" :key="book.id">
        <div class="row">
          <div class="col-md-4">
            <img :src="'http://localhost:8000' + book.file" alt="" class="img-thumbnail" style="border: none">
          </div>
          <div class="col-md-8">
            <b class="text-primary" style="cursor: pointer" @click="viewOneBook(book.id)">{{ book.name }}</b>
            <div class="text-warning" style="text-align: center !important;text-decoration: underline; cursor: pointer"
                 v-if="favorites.indexOf(book.id)!== -1" @click="deleteFavorites(favoritesMap[book.id])">Уже в избранном
            </div>
            <div class="text-warning" style="text-align: center !important;text-decoration: underline; cursor: pointer"
                 v-else @click="addFavorites(book.id)">В избранное
            </div>
            <div class="mt-5">
              <b>Автор</b>: {{ authors[book.author_id] }} <br>
              <b>Возрастные ограничения</b>: {{ book.age }} <br>
              <b>Год</b>: {{ book.year }}<br>
              <b>Категории</b>: <span v-for="tag in book.tags" :key="tag.id">{{ tagsMap[tag] }} </span><br>
            </div>

          </div>
          <div class="col-md-12 mt-4" style="text-align: left !important;">
            <p style="white-space: pre-wrap">
              {{ book.description }}
            </p>
          </div>
          <div class="col-md-12 mt-4" v-if="user_id === 18">
            <div class="row">
              <div class="offset-7 col-md-2">
                <div class="btn btn-primary" @click="bookEdit(book.id)">Редактировать</div>
              </div>
              <div class="offset-1 col-md-2">
                <div class="btn btn-danger" @click="deleteBook(book.id)">Удалить</div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
    <div v-else-if="booksShow === 'create'">
      <div class="container mt-5">
        <div class="col-12">
          <div class="form-group">
            <div class="row">
              <div class="col-4">
                <label for="formName">Название:</label>
              </div>
              <div class="col-8">
                <input type="text" id="formName" class="form-control col-8" v-model="formName" aria-label="Название">
              </div>
            </div>
          </div>
        </div>
        <div class="col-12 my-2">
          <div class="form-group">
            <div class="row">
              <div class="col-4">
                <label for="formAuthorId">Автор:</label>
              </div>
              <div class="col-8">
                <select id="formAuthorId" class="form-control col-8" v-model="formAuthorId">
                  <option v-for="(name, id) in authors" :key="id" :value="id">{{ name }}</option>
                </select>
              </div>
            </div>
          </div>
        </div>
        <div class="col-12 my-2">
          <div class="form-group">
            <div class="row">
              <div class="col-4">
                <label for="formAge">Возрастное ограничение:</label>
              </div>
              <div class="col-8">
                <input type="text" id="formAge" class="form-control col-8" v-model="formAge">
              </div>
            </div>
          </div>
        </div>
        <div class="col-12 my-2">
          <div class="form-group">
            <div class="row">
              <div class="col-4">
                <label for="formYear">Год создания:</label>
              </div>
              <div class="col-8">
                <input type="text" id="formYear" class="form-control col-8" v-model="formYear">
              </div>
            </div>
          </div>
        </div>
        <div class="col-12 my-2">
          <div class="form-group">
            <div class="row">
              <div class="col-4">
                <label for="formDescription">Описание:</label>
              </div>
              <div class="col-8">
                <textarea id="formDescription" class="form-control col-8" v-model="formDescription"></textarea>
              </div>
            </div>
          </div>
        </div>
        <div class="col-12 my-2">
          <div class="form-group">
            <div class="row">
              <div class="col-4">
                <label>Категории:</label>
              </div>
              <div class="col-8">
                <div class="row">
                  <div class="col-auto my-1" v-for="tag in tags" :key="tag.id">
                    <button class="btn" :class="formTagIds.indexOf(tag.id)=== -1?'btn-secondary':'btn-primary'"
                            @click="changeTagStatus(tag.id)">{{ tag.name }}
                    </button>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
        <div class="col-12 my-2">
          <div class="form-group">
            <div class="row">
              <div class="col-4">
                <label for="formFile">Обложка:</label>
              </div>
              <div class="col-8">
                <input type="file" id="formFile" class="form-control col-8" @change="handleFileUpload( $event )"/>
              </div>
            </div>
          </div>
        </div>
        <div class="col-4 btn btn-success mt-4" @click="bookSave">Сохранить</div>
      </div>
    </div>
    <div v-else-if="booksShow === 'view'">
      <div class="offset-md-3 col-md-6 my-5 shadow p-4 mb-5 bg-white rounded " :key="actionBook.id">
        <div class="row">
          <div class="col-md-4">
            <img :src="'http://localhost:8000' + actionBook.file" alt="" class="img-thumbnail" style="border: none">
          </div>
          <div class="col-md-8">
            <b class="text-primary" style="cursor: pointer" @click="viewOneBook(actionBook.id)">{{ actionBook.name }}</b>
            <div class="text-warning" style="text-align: center !important;text-decoration: underline; cursor: pointer"
                 v-if="favorites.indexOf(actionBook.id)!== -1" @click="deleteFavorites(favoritesMap[actionBook.id])">Уже в избранном
            </div>
            <div class="text-warning" style="text-align: center !important;text-decoration: underline; cursor: pointer"
                 v-else @click="addFavorites(actionBook.id)">В избранное
            </div>
            <div class="mt-5">
              <b>Автор</b>: {{ authors[actionBook.author_id] }} <br>
              <b>Возрастные ограничения</b>: {{ actionBook.age }} <br>
              <b>Год</b>: {{ actionBook.year }}<br>
              <b>Категории</b>: <span v-for="tag in actionBook.tags" :key="tag.id">{{ tagsMap[tag] }} </span><br>
            </div>

          </div>
          <div class="col-md-12 mt-4" style="text-align: left !important;">
            <p style="white-space: pre-wrap">
              {{ actionBook.description }}
            </p>
          </div>

          <div class="col-md-12 mt-4" style="text-align: left !important;">
            <h3>Отзывы</h3>
            <div class="row" v-if="!reviewForm">
              <div class="col-11 offset-1 shadow p-4 my-2" v-for="review in reviews" :key="review.id">
                <b>Оценка:</b> {{ review.grade }} <br>
                <b>Комментарий:</b> <span style="white-space: pre-wrap">{{ review.description }}</span> <br>
                <div class="mt-4">Оцените полезен ли комментарий:</div>
                <div class="text-success pl-4 row mt-3">
                  <div class="btn btn-success col-4" @click="reviewsGradesSet(review.id,1)">Полезен
                    ({{ review.positive }})
                  </div>
                  <div class="btn btn-danger col-4 offset-1" @click="reviewsGradesSet(review.id,2)">Не полезен
                    ({{ review.negative }})
                  </div>

                </div>
                <div class="col-12 mt-5" v-if="user_id === review.user_id">
                  <hr>
                  <div class="mt-3 row">
                    <div class="col-3 offset-6 btn btn-primary" @click="reviewEdit(review.id)">Редактировать</div>
                    <div class="col-2 offset-1 btn btn-danger ml-3" @click="reviewDelete(review.id)">Удалить</div>
                  </div>
                </div>
              </div>
            </div>
            <div v-else>
              <div class="row" v-if="reviewForm">
                <div class="col-12">
                  <div class="form-group">
                    <div class="row">
                      <div class="col-4">
                        <label for="reviewGrade">Оценка:</label>
                      </div>
                      <div class="col-8">
                        <input type="number" min="0" max="10" id="reviewGrade" class="form-control col-8"
                               v-model="reviewGrade" aria-label="Название">
                      </div>
                    </div>
                  </div>
                </div>
                <div class="col-12 my-2">
                  <div class="form-group">
                    <div class="row">
                      <div class="col-4">
                        <label for="reviewDescription">Описание:</label>
                      </div>
                      <div class="col-8">
                          <textarea id="reviewDescription" class="form-control col-8"
                                    v-model="reviewDescription"></textarea>
                      </div>
                    </div>
                  </div>
                </div>
              </div>
            </div>

            <div class="row mt-5">
              <div class="col-12">
                <div class="btn btn-primary" v-if="!reviewForm" @click="reviewForm = true">Добавить</div>

                <div class="offset-5 col-4 btn btn-success" v-if="reviewForm" @click="saveReview()">Cохранить</div>
                <div class="offset-1 col-2 btn btn-primary ml-5" v-if="reviewForm" @click="reviewForm = афдыу">
                  Отменить
                </div>
              </div>
            </div>

          </div>

        </div>
      </div>
    </div>
  </div>
</template>

<style>
</style>

<script>
export default {
  name: 'BooksGet',
  props: {
    user_id: {
      type: Number,
      required: true,
    }
  },
  watch: {
    filterTags: function (val, oldVal) {
      console.log(oldVal)
      if (this.formTagIdsFilter.indexOf(val) === -1)
        this.formTagIdsFilter.push(val)
      else {
        this.formTagIdsFilter.splice(this.formTagIdsFilter.indexOf(val), 1)
      }
      this.booksGet()
    },
  },
  data() {
    return {
      actionBookID: 0,
      actionBook: {},
      reviews: [],
      reviewsGrades: [],

      filterName: "",
      filterTags: 0,
      filterYear: "",
      filterAuthor: 0,

      favorites: [],
      favoritesMap: [],

      reviewForm: false,
      reviewGrade: 0,
      reviewDescription: "",
      reviewFormID: 0,

      formID: 0,
      formName: "",
      formFile: "",
      formDescription: "",
      formAuthorId: 1,
      formYear: '2022',
      formAge: "16+",
      formTagIds: [],
      formTagIdsFilter: [],

      booksShow: "all",
      books: [],
      authors: [],
      tags: [],
      tagsMap: [],
      menuClick: "books"
    }
  },
  mounted() {
    this.booksGet()
    this.getFavorites()
  },
  methods: {
    changeTagStatusFilter: function (val) {
      if (this.formTagIdsFilter.indexOf(val) === -1)
        this.formTagIdsFilter.push(val)
      else{
        this.formTagIdsFilter.splice(this.formTagIdsFilter.indexOf(val), 1)
      }
      this.booksGet()
    },
    handleFileUpload(e) {
      this.formFile = e.target.files[0];
    },
    bookEdit(id) {
      this.books.map(book => {
        if (id === book.id) {
          this.formID = book.id
          this.formName = book.name
          this.formDescription = book.description
          this.formAuthorId = book.author_id
          this.formYear = book.year
          this.formAge = book.age
          this.formFile = ""
          this.formTagIds = book.tags
        }
      })
      this.booksShowChange('create')
    },
    async deleteFavorites(favorite_id) {
      await fetch('http://localhost:8000/books/favorite/delete/' + favorite_id,{
        method: "POST"
      })
      await this.getFavorites()
    },
    async addFavorites(book_id) {
      await fetch('http://localhost:8000/books/favorite', {
        method: "POST",
        body: JSON.stringify({
          book_id: book_id,
          user_id: this.user_id
        })
      })

      await this.getFavorites()
    },
    booksShowChange(msg) {
      this.actionBookID = 0
      this.actionBook = []

      this.booksShow = msg
      if (msg === 'all') {
        this.formID = 0
        this.formName = ''
        this.formDescription = ''
        this.formAuthorId = 1
        this.formYear = '2022'
        this.formAge = "16+"
        this.formFile = ""
        this.formTagIds = []
      }
    },
    reviewEdit(id) {
      this.reviewForm = true

      this.reviews.map(r => {
        if (r.id === id) {
          this.reviewFormID = id
          this.reviewGrade = r.grade
          this.reviewDescription = r.description
        }
      })
    },
    viewOneBook(id) {
      console.log(id)
      this.booksShowChange('view')
      this.actionBookID = id
      this.books.map(b => {
        if (b.id === this.actionBookID) {
          this.actionBook = b
        }
      })
      this.reviewsGet()
    },
    booksGet() {
      fetch('http://localhost:8000/books?limit=10000000&offset=0&author_id='+this.filterAuthor + '&name='+ this.filterName + '&year='+this.filterYear + "&tags="+this.formTagIdsFilter)
          .then(res => res.json())
          .then(res => {
            this.books = res.data
          })

      this.tagsGet()
      this.authorsGet()
    },
    async reviewsGet() {
      this.reviewFormID = 0
      this.reviewGrade = 0
      this.reviewDescription = ""
      await fetch('http://localhost:8000/books/reviews?limit=10000000&offset=0&book_id=' + this.actionBookID)
          .then(res => res.json())
          .then(res => {
            this.reviews = res.data
          })
      // await this.reviewsGradesGet()
    },
    async reviewsGradesGet() {
      await fetch('http://localhost:8000/books/reviews/grades/' + this.user_id + "/" + this.actionBookID)
          .then(res => res.json())
          .then(res => {
            console.log(res.data)
            res.data.map(r => {
              this.reviewsGrades[r.book_id] = r.user_id
            })
          })
    },
    async getFavorites() {
      await fetch('http://localhost:8000/books/favorite/' + this.user_id)
          .then(res => res.json())
          .then(res => {
            this.favorites = []
            this.favoritesMap = []
            res.data.map(r => {
              this.favorites.push(r.book_id)
              this.favoritesMap[r.book_id] = r.id
            })
          })
    },
    async reviewsGradesSet(review_id, status) {
      if (confirm('Вы точно хотите оценить отзыв?')) {
        await fetch('http://localhost:8000/books/reviews/grades', {
          method: "POST",
          body: JSON.stringify({
            user_id: this.user_id,
            book_id: this.actionBookID,
            status: status,
            review_id: review_id,
          })
        })

        await this.reviewsGet()
      }
    },
    tagsGet() {
      fetch('http://localhost:8000/tags?limit=10000000&offset=0')
          .then(res => res.json())
          .then(res => {
            this.tags = res.data
            this.tags.map(t => {
              this.tagsMap[t.id] = t.name
            })
          })
    },
    authorsGet() {
      fetch('http://localhost:8000/authors?limit=1000000')
          .then(res => res.json())
          .then(res => {
            res.data.map(author => {
              this.authors[author.id] = author.name
            })
          })
    },
    changeTagStatus(id) {
      if (this.formTagIds.indexOf(id) === -1)
        this.formTagIds.push(id)
      else
        this.formTagIds.splice(this.formTagIds.indexOf(id), 1)
    },

    async deleteBook(book_id) {
      if (confirm('Вы точно хотите удалить книгу?')) {
        await fetch('http://localhost:8000/books/delete/' + book_id,
            {
              method: 'POST'
            })
        await this.booksGet()
      }

    },
    async reviewDelete(id) {
      if (confirm('Вы точно хотите удалить отзыв?')) {
        await fetch('http://localhost:8000/books/reviews/delete/' + id,
            {
              method: 'POST'
            })
        await this.reviewsGet()
      }

    },
    async bookSave() {
      if (!confirm('Вы точно хотите сохранить изменения книги?')) {
        return
      }
      var path = 'http://localhost:8000/books/store'
      if (this.formID !== 0) {
        path = 'http://localhost:8000/books/update/' + this.formID
      }

      const fd = new FormData();
      fd.append('file_upload', this.formFile);

      var id = 0
      await fetch(path, {
        method: "post",
        body: JSON.stringify(
            {
              name: this.formName,
              description: this.formDescription,
              author_id: this.formAuthorId,
              age: this.formAge,
              year: this.formYear,
            }
        )
      }).then(res => res.json()).then(res => {
        id = res.data.id
      })

      await fetch('http://localhost:8000/books/files/' + id, {
        method: "post",
        body: fd
      })

      await fetch('http://localhost:8000/books/tags/delete', {
        method: "post",
        body: JSON.stringify({
          book_id: id,
          tag_id: this.formTagIds
        })
      })

      await fetch('http://localhost:8000/books/tags', {
        method: "post",
        body: JSON.stringify({
          book_id: id,
          tag_id: this.formTagIds
        })
      })

      await this.booksGet()
      this.booksShowChange('all')
    },
    async saveReview() {
      if (!confirm('Вы точно хотите сохранить?')) {
        return
      }
      var path = 'http://localhost:8000/books/reviews/store'
      if (this.reviewFormID !== 0) {
        path = 'http://localhost:8000/books/reviews/update/' + this.reviewFormID
      }

      await fetch(path, {
        method: "post",
        body: JSON.stringify(
            {
              user_id: this.user_id,
              description: this.reviewDescription,
              book_id: this.actionBookID,
              grade: parseInt(this.reviewGrade),
              positive: this.actionBook.positive,
              negative: this.actionBook.negative,
            }
        )
      })

      await this.reviewsGet()
      this.reviewForm = false
    }
  }

}
</script>