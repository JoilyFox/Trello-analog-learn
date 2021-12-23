<template>
    <div class="container">
<!--      Alert-->
        <div class="alert alert-danger" role="alert" v-if="errored">
            Data loading error! <br>
            {{errors[0]}}
        </div>
<!--        Title-->
        <h1>{{name}}</h1>
<!--      Btns-->
        <button type="button" class="btn btn-primary mb-2" @click="createDeskListForm = true" v-if="!createDeskListForm">
            <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" fill="currentColor" class="bi bi-plus-lg" viewBox="0 0 16 16">
                <path fill-rule="evenodd" d="M8 2a.5.5 0 0 1 .5.5v5h5a.5.5 0 0 1 0 1h-5v5a.5.5 0 0 1-1 0v-5h-5a.5.5 0 0 1 0-1h5v-5A.5.5 0 0 1 8 2Z"></path>
            </svg>
            Create new desk
        </button>
        <button type="button" class="btn btn-primary mb-2" @click="changeDeskNameForm = true" v-if="!changeDeskNameForm">
            <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" fill="currentColor" class="bi bi-pencil-fill" viewBox="0 0 16 16">
                <path d="M12.854.146a.5.5 0 0 0-.707 0L10.5 1.793 14.207 5.5l1.647-1.646a.5.5 0 0 0 0-.708l-3-3zm.646 6.061L9.793 2.5 3.293 9H3.5a.5.5 0 0 1 .5.5v.5h.5a.5.5 0 0 1 .5.5v.5h.5a.5.5 0 0 1 .5.5v.5h.5a.5.5 0 0 1 .5.5v.207l6.5-6.5zm-7.468 7.468A.5.5 0 0 1 6 13.5V13h-.5a.5.5 0 0 1-.5-.5V12h-.5a.5.5 0 0 1-.5-.5V11h-.5a.5.5 0 0 1-.5-.5V10h-.5a.499.499 0 0 1-.175-.032l-.179.178a.5.5 0 0 0-.11.168l-2 5a.5.5 0 0 0 .65.65l5-2a.5.5 0 0 0 .168-.11l.178-.178z"/>
            </svg>
            Change desk name
        </button>
<!--      Change desk name form-->
        <form @submit.prevent="saveName" v-if="changeDeskNameForm">
            <div class="form-row row">
                <div class="col-md-12">
                    <label for="validationServer03">Change desk name</label>
                    <div class="row">
                        <div class="col-md-6 mb-2">
                            <input type="text" v-model="name" class="form-control" :class="{ 'is-invalid': $v.name.$error }">
                        </div>
                        <div class="col-md-3 mb-3 pl-0">
                            <button class="btn btn-primary mr-8" type="submit">Change desk name</button>
                            <button class="btn btn-danger" @click="changeDeskNameForm = false">Cancel</button>
                        </div>
                    </div>
                    <div class="invalid-feedback" v-if="!$v.name.required">
                        Obligatory field!
                    </div>
                    <div class="invalid-feedback" v-if="!$v.name.maxLength">
                        Max number of characters is {{$v.name.$params.maxLength.max}}!
                    </div>
                </div>
            </div>
        </form>
<!--      Create card form-->
        <form @submit.prevent="addNewDeskList" v-if="createDeskListForm">
            <div class="form-row row">
                <div class="col-md-12">
                    <label for="validationServer03">Name</label>
                    <div class="row">
                        <div class="col-md-6 mb-2">
                            <input type="text" v-model="desk_list_name" class="form-control" :class="{ 'is-invalid': $v.desk_list_name.$error }" id="validationServer03">
                        </div>
                        <div class="col-md-3 mb-3 pl-0">
                            <button class="btn btn-primary mr-8" type="submit">Create new desk</button>
                            <button class="btn btn-danger" @click="createDeskListForm = false">Cancel</button>
                        </div>
                        <div class="invalid-feedback" v-if="!$v.desk_list_name.required">
                            Obligatory field!
                        </div>
                        <div class="invalid-feedback" v-if="!$v.desk_list_name.maxLength">
                            Max number of characters is {{$v.desk_list_name.$params.maxLength.max}}!
                        </div>
                    </div>
                </div>
            </div>
        </form>
<!--      Loading-->
        <div class="text-center" v-if="loading">
            <div class="spinner-grow text-primary" role="status">
            </div>
        </div>
<!--        List cards-->
        <div class="row">
            <div class="col-lg-4" v-for="desk_list in desk_lists">
                <div class="card mt-3">
<!--                    Card name-->
                    <div class="card-body">
                        <h4 class="card-title">{{ desk_list.name }}</h4>
                        <form @submit.prevent="updateDesklist(desk_list.id, desk_list.name)" v-if="desk_list_input_id == desk_list.id" >
                            <div class="desk-list-edit-form">
                                <input type="text" v-model="desk_list.name" class="form-control mt-3">
                                <button type="submit" class="btn btn-primary" >
                                    <i class="fas fa-pen"></i>
                                </button>
                                <button type="button" class="btn btn-danger" @click="desk_list_input_id = null">
                                    <i class="fas fa-times-circle"></i>
                                </button>
                            </div>
                        </form>
                        <!--                    Buttons-->
                        <div class="desk-list-btns">
                            <button type="button" class="btn btn-primary desk-icon" @click="desk_list_input_id = desk_list.id">
                                <i class="fas fa-pen"></i>
                            </button>
                            <button type="button" class="btn btn-danger desk-icon" @click="deleteDeskList(desk_list.id)">
                                <i class="fas fa-trash"></i>
                            </button>
                        </div>
                        <!--                    Body-->
                        <div class="card mt-3 bg-light">
                            <div class="card-body">
                                <h4 class="card-title">fff</h4>
<!--                                Buttons-->
                                <div class="desk-list-btns">
                                    <button type="button" class="btn btn-secondary desk-icon">
                                        <i class="fas fa-pen"></i>
                                    </button>
                                    <button type="button" class="btn btn-danger desk-icon">
                                        <i class="fas fa-trash"></i>
                                    </button>
                                </div>
                            </div>
                        </div>
                        <form class="d-flex justify-content-between align-items-center mt-3">
                            <input type="text" class="form-control" placeholder="Enter card name">
                        </form>
                    </div>
                </div>
            </div>
        </div>

    </div>
</template>

<script>
import { required, maxLength } from 'vuelidate/lib/validators'
export default {
    props: [
        'deskId'
    ],
    data(){
        return {
            name: null,
            desk_list_name: null,
            errored: false,
            loading: true,
            desk_lists: true,
            createDeskListForm: false,
            errors: [],
            changeDeskNameForm: false,
            desk_list_input_id: null,
        }
    },
    methods: {
        updateDesklist(id, name) {
            console.log(id+', '+name)
            axios.post('/api/v1/desk-lists/'+id, {
                _method: 'PUT',name,
            })
                .then(response => {
                    this.desk_list_input_id = null
                })
                .catch(error => {
                    console.log(error)
                    this.errored = true
                })
                .finally(() => {
                    this.loading = false
                })
        },
        getDeskLists() {
            axios.get('/api/v1/desk-lists', {
                params: {
                    desk_id: this.deskId
                }
            })
                .then(response => {
                    console.log()
                    this.desk_lists = response.data.data
                })
                .catch(error => {
                    console.log(error)
                    this.errored = true
                })
                .finally(() => {
                    this.loading = false
                })
        },
        saveName(){
            axios.post('/api/v1/desks/'+this.deskId, {
                _method: 'PUT',
                name: this.name,
            })
                .then(response => {
                    this.changeDeskNameForm = false
                })
                .catch(error => {
                    console.log(error)
                    this.errored = true
                })
                .finally(() => {
                    this.loading = false
                })

        },
        addNewDeskList(){
            axios.post('/api/v1/desk-lists', {
                name: this.desk_list_name,
                desk_id: this.deskId,
            })
                .then(response => {
                    this.createDeskListForm = false
                    this.desk_list_name = ''
                    this.desk_lists = []
                    this.getDeskLists()
                })
                .catch(error => {
                    console.log(error.response)
                    this.errored = true
                })
                .finally(() => {
                    this.loading = false
                })


        },
        deleteDeskList(id){
            if (confirm('Are you sure you want to delete the desk?')){
                axios.post('/api/v1/desk-lists/'+id, {
                    _method: 'DELETE'
                })
                    .then(response => {
                        this.desk_lists = []
                        this.getDeskLists()
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
    },
    mounted() {
        axios.get('/api/v1/desks/'+this.deskId)
            .then(response => {
                this.name = response.data.data.name
            })
            .catch(error => {
                console.log(error)
                this.errored = true
            })
            .finally(() => {
                this.loading = false
            })
        this.getDeskLists()
    },
    validations: {
        name: {
            required,
            maxLength: maxLength(255)
        },
        desk_list_name: {
            required,
            maxLength: maxLength(255)
        }
    }
}
</script>
