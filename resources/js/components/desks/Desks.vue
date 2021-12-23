<template>
  <div class="container">
      <div class="d-flex flex-lg-row">
          <h1 class="mr-2">Desks</h1>
      </div>
<!--      Alert-->
      <div class="alert alert-danger" role="alert" v-if="errored">
          Data loading error! <br>
          {{errors[0]}}
      </div>
<!--      Loading-->
      <div class="text-center" v-if="loading">
          <div class="spinner-grow text-primary" role="status">
          </div>
      </div>
<!--      Create card btn-->
      <button type="button" class="btn btn-primary mb-2" @click="showCreateDeskForm()" v-if="!createDeskForm">
          <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" fill="currentColor" class="bi bi-plus-lg" viewBox="0 0 16 16">
              <path fill-rule="evenodd" d="M8 2a.5.5 0 0 1 .5.5v5h5a.5.5 0 0 1 0 1h-5v5a.5.5 0 0 1-1 0v-5h-5a.5.5 0 0 1 0-1h5v-5A.5.5 0 0 1 8 2Z"></path>
          </svg>
          Create new desk
      </button>
<!--      Create card form-->
      <form @submit.prevent="addNewDesk" v-if="createDeskForm">
          <div class="form-row row">
              <div class="col-md-12">
                  <label for="validationServer03">Name</label>
                  <div class="row">
                      <div class="col-md-6 mb-2">
                          <input type="text" v-model="name" class="form-control" :class="{ 'is-invalid': $v.name.$error }" id="validationServer03">
                          <div class="invalid-feedback" v-if="!$v.name.required">
                              Obligatory field!
                          </div>
                          <div class="invalid-feedback" v-if="!$v.name.maxLength">
                              Max number of characters is {{$v.name.$params.maxLength.max}}!
                          </div>
                      </div>
                      <div class="col-md-3 mb-3 pl-0">
                          <button class="btn btn-primary mr-8" type="submit">Create new desk</button>
                          <button class="btn btn-danger" @click="hideCreateDeskForm">Cancel</button>
                      </div>
                  </div>
              </div>
          </div>
      </form>
<!--      Cards-->
      <div class="row">
          <div class="col-lg-4" v-for="desk in desks">
              <div class="card mt-3">
                  <router-link class="card-body" :to="{name: 'showDesk', params: {deskId: desk.id}}">
                      <h4 class="card-title">{{ desk.name }}</h4>
                  </router-link>
                  <div class="desk-list-btns">
                      <button type="button" class="btn btn-danger desk-icon" @click="deleteDesk(desk.id)">
                          <i class="fas fa-trash"></i>
                      </button>
                  </div>
              </div>
          </div>
      </div>
  </div>
</template>

<script>
import { required, maxLength } from 'vuelidate/lib/validators'
export default {
    data(){
        return {
            desks: [],
            errored: false,
            loading: true,
            errors: [],
            createDeskForm: false,
            name: null,
        }
    },
    mounted() {
        this.getAllDesks()
    },
    methods: {
        getAllDesks(){
            this.loading = true
            axios.get('/api/v1/desks')
                .then(response => {
                    this.desks = response.data.data
                })
                .catch(error => {
                    console.log(error)
                    this.errored = true
                })
                .finally(() => {
                    this.loading = false
                })

        },
        deleteDesk(id){
            if (confirm('Are you sure you want to delete the desk?')){
                axios.post('/api/v1/desks/'+id, {
                    _method: 'DELETE'
                })
                    .then(response => {
                        this.desks = []
                        this.getAllDesks()
                    })
                    .catch(error => {
                        console.log(error)
                        this.errored = true
                    })
                    .finally(() => {
                        this.loading = false
                    })

            }
        },
        showCreateDeskForm(){
            this.createDeskForm = true
        },
        hideCreateDeskForm(){
            this.createDeskForm = false
        },
        addNewDesk(){
            this.$v.$touch()
            if(this.$v.$anyError) {
                return;
            }
            axios.post('/api/v1/desks', {
                name: this.name,
            })
                .then(response => {
                    this.desks = []
                    this.getAllDesks()
                    this.name = ''
                })
                .catch(error => {
                    console.log(error)
                    if (error.response.data.errors.name) {
                        this.errors = []
                        this.errors.push(error.response.data.errors.name[0])
                    }
                    this.errored = true
                })
                .finally(() => {
                    this.loading = false
                })


        },

    },
    validations: {
        name: {
            required,
            maxLength: maxLength(255)
        }
    },
}
</script>
