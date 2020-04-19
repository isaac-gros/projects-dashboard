<template>
    <form class="row">
        <div class="col-md-6 col-12" >
            <md-field>
                <label for="title">Titre du projet</label>
                <md-input
                    required
                    name="title"
                    v-model="object.title"
                    :disabled="object.preventEdit"/>
            </md-field>
        </div>

        <div class="col-md-6 col-12">
            <md-datepicker name="endDate" v-model="object.endDate" :disabled="object.preventEdit">
                <label for="endDate">Échéance (au format jj/mm/aaaa)</label>
            </md-datepicker>
        </div>

        <div class="col-md-6 col-12">
            <md-field>
                <label for="status">Statut</label>
                <md-select name="status" v-model="object.status" :disabled="object.preventEdit">
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
                <md-select name="clients" v-model="object.clients" :disabled="object.preventEdit" multiple>
                    <md-option value="client_id_0">Fausse valeure</md-option>
                    <md-option value="client_id_1">Une autre fausse valeure</md-option>
                </md-select>
            </md-field>
        </div>
    </form>
</template>

<script>
import { validationMixin } from 'vuelidate'
import { required } from 'vuelidate/lib/validators'

export default {
    name: "ProjectForm",

    // Component props
    props: {
        "object": {
            type: Object,
            required: true
        }
    },

    mounted() {
        console.log("Mounted, invalid = " + this.object.invalid)
        this.object.invalid = true
    },

    // Check fields
    updated() {
        this.object.invalid = (this.object.title == '') ? true : false
    },

    // Update parent
    watch: {
        value() {
            this.$emit('input', this.object);
        }
    }
}
</script>