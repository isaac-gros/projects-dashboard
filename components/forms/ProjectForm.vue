<template>
    <form class="row">
        <div class="col-md-6 col-12" >
            <md-field :class="getValidationClass('title')">
                <label for="title">Titre du projet</label>
                <md-input
                    required
                    name="title"
                    v-model="object.title"/>
                <span class="md-error" v-if="$v.object.title">Ce champ est requis.</span>
            </md-field>
        </div>

        <div class="col-md-6 col-12">
            <md-datepicker name="endDate" v-model="object.endDate">
                <label for="endDate">Échéance (au format jj/mm/aaaa)</label>
            </md-datepicker>
        </div>

        <div class="col-md-6 col-12">
            <md-field>
                <label for="status">Statut</label>
                <md-select name="status" v-model="object.status">
                    <md-option value="todo">À faire</md-option>
                    <md-option value="work-in-progress">En cours</md-option>
                    <md-option value="done">Terminé</md-option>
                    <md-option value="need-fix">Corrections demandées</md-option>
                </md-select>
            </md-field>
        </div>

        <div class="col-md-6 col-12">
            <md-field>
                <label for="clients">Commanditaire(s)</label>
                <md-select name="clients" v-model="object.clients" multiple>
                    <md-option value="client_id_0">Fausse valeure</md-option>
                    <md-option value="client_id_1">Une autre fausse valeure</md-option>
                </md-select>
            </md-field>
        </div>
    </form>
</template>

<script>
 import { validationMixin } from 'vuelidate'
import {
    required,
} from 'vuelidate/lib/validators'

export default {
    name: "ProjectForm",

    // Component props
    props: {
        "object": {
            type: Object,
            required: true
        }
    },

    // Validation
    mixins: [validationMixin],
    validations: {
        object: {
            title: { required }
        }
    },

    methods: {

        // Check fields
        getValidationClass(fieldName) {
            const field = this.$v.object[fieldName]
            if (field) {
                return {
                    'md-invalid': field.$invalid && field.$dirty
                }
            }
        },
    },

    // Check fields
    updated() {
        this.$v.$touch()
        this.object.invalid = this.$v.$invalid
    },

    // Update parent
    watch: {
        value() {
            this.$emit('input', this.object);
        }
    }
}
</script>