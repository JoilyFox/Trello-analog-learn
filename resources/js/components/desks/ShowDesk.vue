<template>
    <div class="container">
        <div class="form-group">
            <input type="text" @blur="saveName" v-model="name" class="form-control mt-2" :class="{ 'is-invalid': $v.name.$error }">
            <div class="invalid-feedback" v-if="!$v.name.required">
                Obligatory field!
            </div>
            <div class="invalid-feedback" v-if="!$v.name.maxLength">
                Max number of characters is {{$v.name.$params.maxLength.max}}!
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
            errored: false,
            loading: true
        }
    },
    methods: {
        saveName(){
            this.$v.$touch()
            if(this.$v.$anyError) {
                return;
            }
            axios.post('/api/v1/desks/'+this.deskId, {
                _method: 'PUT',
                name: this.name,
            })
                .then(response => {
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
    },
    validations: {
        name: {
            required,
            maxLength: maxLength(255)
        }
    }
}
</script>
