<template>
    <form class="row">
        <div class="col-lg-4 col-md-6 col-12">
            <md-field>
                <label for="name">Nom du client</label>
                <md-input
                    required
                    name="name"
                    v-model="object.name"
                    :disabled="object.preventEdit"/>
            </md-field>
        </div>
        
        <div class="col-lg-4 col-md-12 col-12">
            <md-field>
                <label for="phone">Téléphone</label>
                <md-input
                    name="phone"
                    v-model="object.phone"
                    :disabled="object.preventEdit"/>
            </md-field>
        </div>
        
        <div class="col-lg-4 col-md-6 col-12">
            <md-field>
                <label for="mail">Adresse e-mail</label>
                <md-input
                    name="mail"
                    v-model="object.mail"
                    :disabled="object.preventEdit"/>
            </md-field>
        </div>
    </form>
</template>

<script>
import { validationMixin } from 'vuelidate'
import { required } from 'vuelidate/lib/validators'

export default {
    name: "ClientForm",

    // Component props
    props: {
        "object": {
            type: Object,
            required: true
        }
    },

    mounted() {
        this.object.invalid = true
    },

    // Check fields
    updated() {
        this.object.invalid = (this.object.name == '') ? true : false
    },

    // Update parent
    watch: {
        value() {
            this.$emit('input', this.object);
        }
    }
}
</script>