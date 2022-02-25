<template>
  <div class="col-md-12">
    <div class="row my-3">
      <div class="text-right col-md-2 offset-md-10">
        <div v-if="tagsShow === 'all'" class="btn btn-success" @click="tagsShowChange('create')">Добавить</div>
        <div v-if="tagsShow === 'create'" class="btn btn-primary" @click="tagsShowChange('all')">Просмотр</div>
      </div>
    </div>
    <table class="table table-bordered" v-if="tagsShow === 'all'">
      <thead>
      <tr>
        <td>#</td>
        <td>Название</td>
        <td></td>
      </tr>
      </thead>
      <tbody>
      <tr v-for="tag in tags" :key="tag.id">
        <td>{{tag.id}}</td>
        <td>{{tag.name}}</td>
        <td>
          <div class="btn btn-primary" @click="tagEdit(tag.id)">Редактировать</div>
        </td>
      </tr>
      </tbody>
    </table>
    <div v-else-if="tagsShow === 'create'">
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
        <div class="col-4 btn btn-success mt-4" @click="tagSave">Сохранить</div>
      </div>
    </div>
  </div>
</template>

<style>
</style>

<script>
export default {
  name: 'tagsGet',
  data() {
    return {
      formID: 0,
      formName: "",

      tagsShow: "all",
      tags: [],
    }
  },
  mounted() {
    this.tagsGet()
  },
  methods: {
    tagEdit(id) {
      this.tags.map(tag => {
        if (id === tag.id) {
          this.formID = tag.id
          this.formName = tag.name
        }
      })
      this.tagsShowChange('create')
    },

    tagsShowChange(msg) {
      this.tagsShow = msg
      if (msg === 'all') {
        this.formID = 0
        this.formName = 'Название'
      }
    },
    tagsGet() {
      fetch('http://localhost:8000/tags?limit=1000000&offset=0')
          .then(res => res.json())
          .then(res => {
            this.tags = res.data
          })
    },
    async tagSave() {
      var path = 'http://localhost:8000/tags/store'
      if (this.formID !== 0) {
       path = 'http://localhost:8000/tags/update/' + this.formID
      }
      await fetch(path, {
          method: "post",
          body: JSON.stringify(
              {
                name: this.formName,
              }
          )
        })

      await this.tagsGet()
      this.tagsShowChange('all')
    }
  }

}
</script>