<template>
    <div class="forms_container__form">
        <div name="login_form" class="login_form">

            <h1>Connectez-vous</h1>

            <md-progress-spinner 
                class="md-primary"
                md-mode="indeterminate"
                v-if="loginFunctionActive">
            </md-progress-spinner>

            <md-field :class="getValidationClass('email')">
                <label for="email_login">Adresse e-mail</label>
                <md-input 
                    id="email_login"
                    name="email_login"
                    v-model="email"/>
                <span class="md-error" v-if="!$v.email.required">Veuillez entrer votre adresse e-mail.</span>
                <span class="md-error" v-else-if="!$v.email.email">L'adresse e-mail saisie est dans un format invalide.</span>
            </md-field>
            
            <md-field :class="getValidationClass('password')">
                <label for="password_login">Mot de passe</label>
                <md-input 
                    id="password_login"
                    name="password_login"
                    type="password"
                    v-model="password"/>
                <span class="md-error" v-if="!$v.password.minLength">Le mot de passe doit faire au minimum 6 caractères.</span>
            </md-field>

            <span v-if="loginErrorMessage">{{ loginErrorMessage }}</span>

            <md-button class="md-raised md-primary" v-on:click="loginUser">
                Connexion
            </md-button>
        </div>
    </div>
</template>

<script>
import { validationMixin } from 'vuelidate'
import {
    required,
    email,
    minLength
} from 'vuelidate/lib/validators'

export default {
    name: "LoginForm",

    // Components props
    data() {
        return {

            // Fields to complete
            email: '',
            password: '',

            // States of the component
            loginErrorMessage: '',
            loginFunctionActive: false
        }
    },

    // Form validation
    mixins: [validationMixin], 
    validations: {
        email: {
            required,
            email
        },
        password: {
            required,
            minLength: minLength(6)
        },
    },

    methods: {

        // Check if the fields are correct
        getValidationClass (fieldName) {
            let field = this.$v[fieldName]
            if(field) {
                return {
                    'md-invalid': field.$invalid && field.$dirty
                }
            }
        },

        // Login process
        loginUser() {
            this.$v.$touch()
            this.loginErrorMessage = ''

            if(!this.$v.invalid) {
                this.$fireAuth.signInWithEmailAndPassword(this.email, this.password)
                .then(user => {
                    if(user) {
                        console.log(user)
                    }
                })
                .catch(error => {
                    this.loginErrorMessage = error.message
                });
            }
        }
    },
}
</script>