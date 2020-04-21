<template>
    <div class="forms_container__form">
        <div name="register_form" class="register_form">

            <h1>
                Créez votre compte
                <md-progress-spinner 
                    class="md-primary"
                    md-mode="indeterminate"
                    md-diameter=20
                    md-stroke=3
                    v-if="registerFunctionActive">
                </md-progress-spinner>
            </h1>
            
            <md-field :class="getValidationClass('email')">
                <label for="email_reg">Adresse e-mail</label>
                <md-input 
                    id="email_reg"
                    name="email_reg"
                    v-model="email"/>
                <span class="md-error" v-if="!$v.email.required">Une adresse e-mail est requise.</span>
                <span class="md-error" v-else-if="!$v.email.email">Format de l'adresse e-mail invalide.</span>
            </md-field>
            
            <md-field :class="getValidationClass('password')" :md-toggle-password="false">
                <label for="password_reg">Mot de passe</label>
                <md-input 
                    id="password_reg"
                    name="password_reg"
                    :type="showPassword ? 'text' : 'password'"
                    v-model="password"/>
                <span class="md-error" v-if="!$v.password.minLength">Le mot de passe doit faire au minimum 6 caractères.</span>
            </md-field>
            
            <md-field :class="getValidationClass('passwordConfirm')" :md-toggle-password="false">
                <label for="password_confirm_reg">Confirmation du mot de passe</label>
                <md-input
                    id="password_confirm_reg"
                    name="password_confirm_reg"
                    :type="showPassword ? 'text' : 'password'"
                    v-model="passwordConfirm"/>
                <span class="md-error" v-if="!$v.passwordConfirm.sameAs">Les mots de passe ne correspondent pas.</span>
            </md-field>

            <md-switch v-model="showPassword">
                Afficher les mots de passe
            </md-switch>

            <p v-if="registerSuccess">Inscription réussie !</p>
            <p v-if="registerErrorMessage">Erreur lors de l'inscription : {{ registerErrorMessage }}</p>

            <md-button class="md-raised md-primary" v-on:click="registerUser">
                Inscription
            </md-button>
        </div>
    </div>
</template>

<script>
import { validationMixin } from 'vuelidate'
import {
    required,
    email,
    sameAs,
    minLength,
} from 'vuelidate/lib/validators'

export default {
    name: "RegisterForm",

    // Component props
    data() {
        return {
            // Fields to complete
            email: '',
            password: '',
            passwordConfirm: '',

            // States of the app
            showPassword: false,
            registerFunctionActive: false,
            registerSuccess: false,
            registerErrorMessage: ''
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
        passwordConfirm: {
            required,
            sameAsPassword: sameAs('password')
        }
    },

    // Component functions
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

        // Show/hide the password
        togglePassword() {
            this.showPassword = !this.showPassword
        },

        // Register an user
        registerUser() {

            // Update app states
            this.$v.$touch()
            this.registerErrorMessage = ''
            this.showPassword,
            this.registerSuccess = false

            // Submit form to Firebase
            if(!this.$v.invalid) {
                this.registerFunctionActive = true
                this.$fireAuth
                    .createUserWithEmailAndPassword(this.email, this.password)
                    .then(userObject => {
                        if(userObject) {
                            this.writeUser(userObject.user.uid, userObject.user.email)
                        }
                    })
                    .catch(error => {
                        if(error) {
                            this.registerErrorMessage = error.message
                            this.registerFunctionActive = false
                        }
                    })
            }
        },

        // Write new user to database
        writeUser(userId, userEmail) {
            this.$fireDb.ref("users/" + userId).set({
                email: userEmail
            }, (error) => {
                if(error) {
                    this.registerFunctionActive = false
                    this.registerErrorMessage = error.message
                } else {
                    this.registerSuccess = true
                    this.registerFunctionActive = false
                    this.$router.push("/dashboard")
                }
            })
        }
    },
}
</script>