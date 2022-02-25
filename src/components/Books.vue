<template>
  <div class="col-md-12">
    <div class="row my-3">
      <div class="text-right col-md-2 offset-md-10">
        <div v-if="booksShow === 'all'" class="btn btn-success" @click="booksShowChange('create')">Добавить</div>
        <div v-if="booksShow === 'create'" class="btn btn-primary" @click="booksShowChange('all')">Просмотр</div>
      </div>
    </div>
    <div class="row" v-if="booksShow === 'all'">
      <div class="offset-md-3 col-md-6 my-5 shadow p-4 mb-5 bg-white rounded " v-for="book in books" :key="book.id">
        <div class="row">
          <div class="col-md-4">
            <img :src="'http://localhost:8000' + book.file" alt="" class="img-thumbnail" style="border: none">
          </div>
          <div class="col-md-8">
            <b class="">{{ book.name }}</b>
            <div class="mt-5">
              <b>Автор</b>: {{ authors[book.author_id] }} <br>
              <b>Возрастные ограничения</b>: {{ book.age }} <br>
              <b>Год</b>: {{ book.year }}<br>
              <b>Категории</b>: <span v-for="tag in book.tags" :key="tag.id">{{tagsMap[tag]}} </span><br>
            </div>

          </div>
          <div class="col-md-12 mt-4" style="text-align: left !important;">
            <p style="white-space: pre-wrap">
              {{ book.description }}
            </p>
          </div>
          <div class="col-md-12 mt-4">
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
                    <button class="btn" :class="formTagIds.indexOf(tag.id)=== -1?'btn-secondary':'btn-primary'" @click="changeTagStatus(tag.id)">{{tag.name}}</button>
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
  </div>
</template>

<style>
</style>

<script>
export default {
  name: 'BooksGet',
  data() {
    return {
      formID: 0,
      formName: "",
      formFile: "",
      formDescription: "",
      formAuthorId: 1,
      formYear: '2022',
      formAge: "16+",
      formTagIds: [],

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
  },
  methods: {
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

    booksShowChange(msg) {
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
    booksGet() {
      fetch('http://localhost:8000/books?limit=10000000&offset=0')
          .then(res => res.json())
          .then(res => {
            this.books = res.data
          })

      this.tagsGet()
      this.authorsGet()
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
    changeTagStatus(id){
      if (this.formTagIds.indexOf(id) === -1)
        this.formTagIds.push(id)
      else
        this.formTagIds.splice(this.formTagIds.indexOf(id), 1)
    },
    async deleteBook(book_id) {
      await fetch('http://localhost:8000/books/delete/' + book_id,
          {
            method: 'POST'
          })
      await this.booksGet()
    },
    async bookSave() {
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
      }).then(res => res.json()).
          then(res => {
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
          tag_id : this.formTagIds
        })
      })

      await fetch('http://localhost:8000/books/tags', {
        method: "post",
        body: JSON.stringify({
          book_id: id,
          tag_id : this.formTagIds
        })
      })

      await this.booksGet()
      this.booksShowChange('all')
    }
  }

}
</script>