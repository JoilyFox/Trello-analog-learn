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
        <button type="button" class="btn btn-primary" @click="createDeskListForm = true, changeDeskNameForm = false" v-if="!createDeskListForm">
            <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" fill="currentColor" class="bi bi-plus-lg" viewBox="0 0 16 16">
                <path fill-rule="evenodd" d="M8 2a.5.5 0 0 1 .5.5v5h5a.5.5 0 0 1 0 1h-5v5a.5.5 0 0 1-1 0v-5h-5a.5.5 0 0 1 0-1h5v-5A.5.5 0 0 1 8 2Z"></path>
            </svg>
            Create new desk
        </button>
        <button type="button" class="btn btn-primary" @click="changeDeskNameForm = true, createDeskListForm = false" v-if="!changeDeskNameForm">
            <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" fill="currentColor" class="bi bi-pencil-fill" viewBox="0 0 16 16">
                <path d="M12.854.146a.5.5 0 0 0-.707 0L10.5 1.793 14.207 5.5l1.647-1.646a.5.5 0 0 0 0-.708l-3-3zm.646 6.061L9.793 2.5 3.293 9H3.5a.5.5 0 0 1 .5.5v.5h.5a.5.5 0 0 1 .5.5v.5h.5a.5.5 0 0 1 .5.5v.5h.5a.5.5 0 0 1 .5.5v.207l6.5-6.5zm-7.468 7.468A.5.5 0 0 1 6 13.5V13h-.5a.5.5 0 0 1-.5-.5V12h-.5a.5.5 0 0 1-.5-.5V11h-.5a.5.5 0 0 1-.5-.5V10h-.5a.499.499 0 0 1-.175-.032l-.179.178a.5.5 0 0 0-.11.168l-2 5a.5.5 0 0 0 .65.65l5-2a.5.5 0 0 0 .168-.11l.178-.178z"/>
            </svg>
            Change desk name
        </button>
<!--      Change desk name form-->
        <form @submit.prevent="saveName" v-if="changeDeskNameForm">
            <div class="form-row row mt-2">
                <div class="col-md-12">
                    <label for="validationServer03">Change desk name</label>
                    <div class="row">
                        <div class="col-md-6 mb-2">
                            <input type="text" v-model="name" class="form-control" :class="{ 'is-invalid': $v.name.$error }">
                            <div class="invalid-feedback" v-if="!$v.name.required">
                                Obligatory field!
                            </div>
                            <div class="invalid-feedback" v-if="!$v.name.maxLength">
                                Max number of characters is {{$v.name.$params.maxLength.max}}!
                            </div>
                        </div>
                        <div class="col-md-6 mb-3 pl-0">
                            <button class="btn btn-primary mr-8" type="submit">Change desk name</button>
                            <button class="btn btn-danger" @click="changeDeskNameForm = false">Cancel</button>
                        </div>
                    </div>
                </div>
            </div>
        </form>
<!--      Create card form-->
        <form @submit.prevent="addNewDeskList" v-if="createDeskListForm">
            <div class="form-row row mt-2">
                <div class="col-md-12">
                    <label for="validationServer03">Name</label>
                    <div class="row">
                        <div class="col-md-6 mb-2">
                            <input type="text" v-model="desk_list_name" class="form-control" placeholder="Create new desk list" :class="{ 'is-invalid': $v.desk_list_name.$error }" id="validationServer03">
                            <div class="invalid-feedback" v-if="!$v.desk_list_name.required">
                                Obligatory field!
                            </div>
                            <div class="invalid-feedback" v-if="!$v.desk_list_name.maxLength">
                                Max number of characters is {{$v.desk_list_name.$params.maxLength.max}}!
                            </div>
                        </div>
                        <div class="col-md-6 mb-3 pl-0">
                            <button class="btn btn-primary mr-8" type="submit">Create new desk</button>
                            <button class="btn btn-danger" @click="createDeskListForm = false">Cancel</button>
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
<!--        Desk Lists-->
        <div class="row">
            <div class="col-lg-4" v-for="(desk_list, index) in desk_lists">
                <div class="card mt-3">
<!--                    Card name-->
                    <div class="card-body">
                        <h4 class="card-title">{{ desk_list.name }}</h4>
                        <form @submit.prevent="updateDesklist(desk_list.id, desk_list.name)" v-if="desk_list_input_id == desk_list.id">
                            <div class="desk-list-edit-form mt-3">
                                <div class="d-flex flex-md-column">
                                    <input type="text" v-model="desk_list.name" class="form-control" :class="{ 'is-invalid': $v.desk_lists.$each[index].name.$error }">
                                    <div class="invalid-feedback" v-if="!$v.desk_lists.$each[index].name.required">
                                        Obligatory field!
                                    </div>
                                    <div class="invalid-feedback" v-if="!$v.desk_lists.$each[index].name.maxLength">
                                        Max number of characters is {{ $v.desk_lists.$each[index].name.$params.maxLength.max }}!
                                    </div>
                                </div>
                                <div class="d-flex pl-0 align-self-start">
                                    <button type="submit" class="btn btn-primary" >
                                        <i class="fas fa-pen"></i>
                                    </button>
                                    <button type="button" class="btn btn-danger" @click="desk_list_input_id = null, getDeskLists()">
                                        <i class="fas fa-times-circle"></i>
                                    </button>
                                </div>
                            </div>
                        </form>
<!--                        Buttons-->
                        <div class="desk-list-btns">
                            <button type="button" class="btn btn-primary desk-icon" @click="desk_list_input_id = desk_list.id">
                                <i class="fas fa-pen"></i>
                            </button>
                            <button type="button" class="btn btn-danger desk-icon" @click="deleteDeskList(desk_list.id)">
                                <i class="fas fa-trash"></i>
                            </button>
                        </div>
<!--                        Cards-->
                        <div class="card mt-3 bg-light" v-for="card in desk_list.cards" :key="card.id">
                            <div class="card-body">
<!--                                Title-->
                                <h4 class="card-title">{{ card.name }}</h4>
<!--                                Buttons-->
                                <div class="desk-list-btns">
                                    <button type="button" @click="deleteCard(card.id)" class="btn btn-danger desk-icon">
                                        <i class="fas fa-trash"></i>
                                    </button>
                                </div>
<!--                                Modal btn-->
                                <button type="button" @click="getCard(card.id)" class="btn btn-primary mt-3" data-bs-toggle="modal" data-bs-target="#staticBackdrop">
                                    Open card
                                </button>
<!--                                Modal-->
                                <div class="modal fade" id="staticBackdrop" data-bs-backdrop="static" data-bs-keyboard="false" tabindex="-1" aria-labelledby="staticBackdropLabel" aria-hidden="true">
                                    <div class="modal-dialog modal-lg">
                                        <div class="modal-content">
                                            <div class="modal-header">
                                                <div class="d-flex flex-lg-row" v-if="!show_card_name_input">
                                                    <h3 class="modal-title" id="staticBackdropLabel">{{ current_card.name }}</h3>
                                                    <span @click="show_card_name_input = true" class="edit-card-btn"><i class="fas fa-pen"></i></span>
                                                </div>
<!--                                                Edit card name-->
                                                <form @submit.prevent="updateCardName" v-if="show_card_name_input">
                                                    <div class="form-row row">
                                                        <div class="col-md-12">
                                                            <label for="validationServer03">Name</label>
                                                            <div class="row">
                                                                <div class="col-md-6 mb-2">
                                                                    <input type="text" v-model="current_card.name" class="form-control" :class="{ 'is-invalid': $v.current_card.name.$error }" id="validationServer03">
                                                                    <div class="invalid-feedback" v-if="!$v.current_card.name.required">
                                                                        Obligatory field!
                                                                    </div>
                                                                    <div class="invalid-feedback" v-if="!$v.current_card.name.maxLength">
                                                                        Max number of characters is {{$v.current_card.name.$params.maxLength.max}}!
                                                                    </div>
                                                                </div>
                                                                <div class="col-md-6 mb-3 pl-0">
                                                                    <button class="btn btn-primary mr-8" type="submit">Change name</button>
                                                                    <button class="btn btn-danger" @click="hide_edit_card_name(card.id)">Cancel</button>
                                                                </div>
                                                            </div>
                                                        </div>
                                                    </div>
                                                </form>
<!--                                                // Edit card name-->
                                                <button @click="show_card_name_input = false, createTaskForm = false, task_input_name_id = null" type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
                                            </div>
<!--                                            Body-->
                                            <div class="modal-body">
                                                <button type="button" class="btn btn-primary mb-2" @click="createTaskForm = true" v-if="!createTaskForm">
                                                    <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" fill="currentColor" class="bi bi-plus-lg" viewBox="0 0 16 16">
                                                        <path fill-rule="evenodd" d="M8 2a.5.5 0 0 1 .5.5v5h5a.5.5 0 0 1 0 1h-5v5a.5.5 0 0 1-1 0v-5h-5a.5.5 0 0 1 0-1h5v-5A.5.5 0 0 1 8 2Z"></path>
                                                    </svg>
                                                    Create new task
                                                </button>
<!--                                                Create task form-->
                                                <form @submit.prevent="addNewTask" v-if="createTaskForm">
                                                    <div class="form-row row">
                                                        <div class="col-md-12">
                                                            <label for="validationServer03">Name</label>
                                                            <div class="row">
                                                                <div class="col-md-6 mb-2">
                                                                    <input type="text" v-model="new_task_name" class="form-control" placeholder="Create new task" :class="{ 'is-invalid': $v.new_task_name.$error }" id="validationServer03">
                                                                    <div class="invalid-feedback" v-if="!$v.new_task_name.required">
                                                                        Obligatory field!
                                                                    </div>
                                                                    <div class="invalid-feedback" v-if="!$v.new_task_name.maxLength">
                                                                        Max number of characters is {{$v.new_task_name.$params.maxLength.max}}!
                                                                    </div>
                                                                </div>
                                                                <div class="col-md-6 mb-3 pl-0">
                                                                    <button class="btn btn-primary mr-8" type="submit">Create new task</button>
                                                                    <button class="btn btn-danger" @click="createTaskForm = false">Cancel</button>
                                                                </div>
                                                            </div>
                                                        </div>
                                                    </div>
                                                </form>


                                                <div class="form-check" v-for="(task, index) in current_card.tasks">
                                                    <div class="d-inline-flex">

                                                        <form @submit.prevent="changeTaskName(task)" v-if="task_input_name_id == task.id">
                                                            <div class="form-row row">
                                                                <div class="col-md-12">
                                                                    <label for="validationServer03">Task name</label>
                                                                    <div class="row">
                                                                        <div class="col-md-6 mb-2">
                                                                            <input type="text" v-model="task.name" class="form-control" id="validationServer03">
                                                                        </div>
                                                                        <div class="col-md-6 mb-3 pl-0">
                                                                            <button class="btn btn-primary mr-8" type="submit">Change name</button>
                                                                            <button class="btn btn-danger" @click="task_input_name_id = null">Cancel</button>
                                                                        </div>
                                                                    </div>
                                                                </div>
                                                            </div>
                                                        </form>

                                                        <div class="d-inline-block" v-else>
                                                            <input class="form-check-input" @change="changeTaskName(task)" v-model="task.is_done" type="checkbox" :id='task.id' value="option1">
                                                            <label class="form-check-label" :for='task.id'>{{ task.name }}</label>
                                                            <div class="btns d-inline-block">
                                                            <span @click="task_input_name_id = task.id">
                                                                <i class="fas fa-pen task-btn"></i>
                                                            </span>
                                                                <span @click="deleteTask(task.id)">
                                                                <i class="fas fa-trash task-btn"></i>
                                                            </span>
                                                            </div>
                                                        </div>
                                                    </div>
                                                </div>


                                            </div>
                                            <div class="modal-footer">
                                                <button @click="show_card_name_input = false, createTaskForm = false, task_input_name_id = null" type="button" class="btn btn-secondary" data-bs-dismiss="modal">Close</button>
                                            </div>
                                        </div>
                                    </div>
                                </div>
<!--                                // modal-->
                            </div>
                        </div>

                        <form @submit.prevent="addNewCard(desk_list.id)" class="d-flex justify-content-between align-items-center mt-3">
                            <div class="col-md-12">
                                <div class="form-row">
                                    <div class="desk-list-edit-form">
                                        <div class="d-flex flex-md-column">
                                            <input type="text" v-model="card_names[desk_list.id]" class="form-control" placeholder="Create new card" :class="{ 'is-invalid': $v.card_names.$each[desk_list.id].$error }">
                                            <div class="invalid-feedback" v-if="!$v.card_names.$each[desk_list.id].required">
                                                Obligatory field!
                                            </div>
                                            <div class="invalid-feedback" v-if="!$v.card_names.$each[desk_list.id].maxLength">
                                                Max number of characters is {{ $v.card_names.$each[desk_list.id].$params.maxLength.max }}!
                                            </div>
                                        </div>
                                        <div class="d-flex pl-0 align-self-start">
                                            <button class="btn btn-primary" type="submit">Create</button>
                                        </div>
                                    </div>
                                </div>
                            </div>
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
            desk_lists: [],
            createDeskListForm: false,
            errors: [],
            changeDeskNameForm: false,
            desk_list_input_id: null,
            card_names: [],
            current_card: [],
            show_card_name_input: false,
            createTaskForm: false,
            new_task_name: '',
            task_input_name_id: null,
            tasks: [],
        }
    },
    methods: {
        changeTaskName(task){
            axios.post('/api/v1/tasks/' + task.id, {
                _method: 'PATCH',
                name: task.name,
                is_done: task.is_done,
                card_id: task.card_id,
            })
                .then(response => {
                    this.$v.$reset()
                    this.task_input_name_id = null
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
        deleteTask(id){
            if (confirm('Are you sure you want to delete this task?')){
                axios.post('/api/v1/tasks/' + id, {
                    _method: 'DELETE'
                })
                    .then(response => {
                        this.$v.$reset()
                        this.getCard(this.current_card.id)
                    })
                    .catch(error => {
                        console.log(error.response)
                        this.errored = true
                    })
                    .finally(() => {
                        this.loading = false
                    })

            }
        },
        addNewTask(desk_list_id){
            this.$v.new_task_name.$touch()
            if(this.$v.new_task_name.$anyError){
                return;
            }
            axios.post('/api/v1/tasks/', {
                name: this.new_task_name,
                card_id: this.current_card.id
            })
                .then(response => {
                    this.$v.$reset()
                    this.new_task_name = ''
                    this.getCard(this.current_card.id)
                })
                .catch(error => {
                    console.log(error)
                    this.errored = true
                })
                .finally(() => {
                    this.loading = false
                })
        },
        hide_edit_card_name(card_id) {
            this.show_card_name_input = false
            this.getCard(card_id)
        },
        updateCardName(){
            this.$v.current_card.name.$touch()
            if(this.$v.current_card.name.$anyError){
                return;
            }
            axios.post('/api/v1/cards/' + this.current_card.id, {
                _method: 'PATCH',
                name: this.current_card.name,
                desk_list_id: this.current_card.desk_list_id,
            })
                .then(response => {
                    this.show_card_name_input = false
                    this.getDeskLists()
                })
                .catch(error => {
                    console.log(error)
                    this.errored = true
                })
                .finally(() => {
                    this.loading = false
                })
        },
        getCard(id){
            axios.get('/api/v1/cards/'+id)
                .then(response => {
                    this.current_card = response.data.data
                    this.tasks = [];
                    this.current_card.tasks.forEach(el => {
                        this.tasks[el.id] = ''
                    })
                    console.log(this.tasks)
                })
                .catch(error => {
                    console.log(error)
                    this.errored = true
                })
                .finally(() => {
                    this.loading = false
                })
        },
        deleteCard(id){
            if (confirm('Are you sure you want to delete this card?')){
                axios.post('/api/v1/cards/'+id, {
                    _method: 'DELETE'
                })
                    .then(response => {
                        this.$v.$reset()
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
        addNewCard(desk_list_id){
            this.$v.card_names.$each[desk_list_id].$touch()
            if(this.$v.card_names.$each[desk_list_id].$anyError){
                return;
            }
            axios.post('/api/v1/cards/', {
                name: this.card_names[desk_list_id],
                desk_list_id
            })
                .then(response => {
                    this.$v.$reset()
                    this.getDeskLists()
                })
                .catch(error => {
                    console.log(error)
                    this.errored = true
                })
                .finally(() => {
                    this.loading = false
                })
        },
        updateDesklist(id, name) {
            this.$v.desk_lists.$touch()
            if(this.$v.desk_lists.$anyError){
                return;
            }
            axios.post('/api/v1/desk-lists/'+id, {
                _method: 'PUT',
                name,
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
                    this.desk_lists = response.data.data
                    this.desk_lists.forEach(el => {
                        this.card_names[el.id] = ''
                    })
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
            this.$v.name.$touch()
            if(this.$v.name.$anyError){
                return;
            }
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
            this.$v.desk_list_name.$touch()
            if(this.$v.desk_list_name.$anyError){
                return;
            }
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
            if (confirm('Are you sure you want to delete the desk list?')){
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
        },
        card_names: {
            $each: {
                required,
                maxLength: maxLength(255)
            }
        },
        current_card: {
            name: {
                required,
                maxLength: maxLength(255)
            }
        },
        desk_lists: {
            $each: {
                name: {
                    required,
                    maxLength: maxLength(255)
                }
            }
        },
        new_task_name: {
            required,
            maxLength: maxLength(255)
        },

    }
}
</script>
