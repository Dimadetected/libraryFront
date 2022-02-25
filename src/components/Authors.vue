<template>
  <div class="col-md-12">
    <div class="row my-3">
      <div class="text-right col-md-2 offset-md-10">
        <div v-if="authorsShow === 'all'" class="btn btn-success" @click="authorsShowChange('create')">Добавить</div>
        <div v-if="authorsShow === 'create'" class="btn btn-primary" @click="authorsShowChange('all')">Просмотр</div>
      </div>
    </div>
    <table class="table table-bordered" v-if="authorsShow === 'all'">
      <thead>
      <tr>
        <th>#</th>
        <th>ФИО</th>
        <th>Дата рождения</th>
        <th></th>
      </tr>
      </thead>
      <tbody>
      <tr v-for="author in authors" :key="author.id">
        <td>{{author.id}}</td>
        <td>{{author.name}}</td>
        <td>{{author.birthday}}</td>
        <td>
          <div class="btn btn-primary" @click="authorEdit(author.id)">Редактировать</div>
        </td>
      </tr>
      </tbody>
    </table>
    <div v-else-if="authorsShow === 'create'">
      <div class="container mt-5">
        <div class="col-12">
          <div class="form-group">
            <div class="row">
              <div class="col-4">
                <label for="formName">ФИО:</label>
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
                <label for="formBirthday">Дата рождения:</label>
              </div>
              <div class="col-8">
                <input type="text" id="formBirthday" class="form-control col-8" v-model="formBirthday">
              </div>
            </div>
          </div>
        </div>
        <div class="col-4 btn btn-success mt-4" @click="authorSave">Сохранить</div>
      </div>
    </div>
  </div>
</template>

<style>
</style>

<script>
export default {
  name: 'authorsGet',
  data() {
    return {
      formID: 0,
      formName: "",
      formBirthday: "01.01.2001",

      authorsShow: "all",
      authors: [],
      menuClick: "authors"
    }
  },
  mounted() {
    this.authorsGet()
  },
  methods: {
    authorEdit(id) {
      this.authors.map(author => {
        if (id === author.id) {
          this.formID = author.id
          this.formName = author.name
          this.formBirthday = author.birthday
        }
      })
      this.authorsShowChange('create')
    },

    authorsShowChange(msg) {
      this.authorsShow = msg
      if (msg === 'all') {
        this.formID = 0
        this.formName = 'ФИО'
        this.formBirthday = '01.01.2001'
      }
    },
    authorsGet() {
      fetch('http://localhost:8000/authors?limit=1000000&offset=0')
          .then(res => res.json())
          .then(res => {
            this.authors = res.data
          })
    },
    async authorSave() {
      var path = 'http://localhost:8000/authors/store'
      if (this.formID !== 0) {
       path = 'http://localhost:8000/authors/update/' + this.formID
      }
      await fetch(path, {
          method: "post",
          body: JSON.stringify(
              {
                name: this.formName,
                birthday: this.formBirthday,
              }
          )
        })

      await this.authorsGet()
      this.authorsShowChange('all')
    }
  }

}
</script>