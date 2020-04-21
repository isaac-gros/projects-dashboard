<template>
	<div class="container" id="client__new">
		<div class="container" id="project__new" :class="creationDone ? 'function_done' : ''">
		<div class="row">
			<div class="col-12">
				<h1 class="section_title">
					Ajouter un nouveau client
					<md-progress-spinner 
						class="md-primary"
						md-mode="indeterminate"
						md-diameter=25
						md-stroke=3
						v-if="creationActive">
					</md-progress-spinner>
					<md-icon
						v-if="creationDone"
						class="md-primary section_title__icon">done</md-icon>
				</h1>
			</div>
			<div class="col-12">
				<div class="container">
					<ClientForm
						:object="client"
						@input="(update) => {project = update}"/>
					<div class="row">
						<div class="col-12">
							<div class="submit_buttons">
								<md-button class="md-raised" v-on:click="cancelClient">
									Annuler
								</md-button>
								<md-button 
									class="md-raised md-primary"
									:disabled="client.invalid" 
									v-on:click="submitClient">
									Créer le client
								</md-button>
							</div>
						</div>
						<div class="col-12" v-if="errorMessage">
							<p>Erreur lors de la création du client : {{ errorMessage }}</p>
						</div>
					</div>
				</div>
			</div>
		</div>
	</div>
	</div>
</template>


<script>
import ClientForm from "~/components/forms/ClientForm.vue"

export default {
	name: "ProjectAdd",
	
	head: {
		titleTemplate: "Ajouter un client",
		meta: [
			{ hid: 'description', name: 'description', content: 'Créez un nouveau client pour vos projets.' }
		]
	},

    components: {
        ClientForm
    },

    data() {
        return {
			// Form default fields
			userId: '',
            client: {
				invalid: true
			},
			
			// States of the component
			creationActive: false,
			creationDone: false,
			errorMessage: ''
        }
    },
    
    methods: {

        // Cancel project creation
        cancelClient() {
            this.$router.back()
        },

        // Submit client to Firebase
        submitClient() {
            if(!this.client.invalid) {
				this.client.preventEdit = true
				this.errorMessage = ''
				this.creationActive = true

				this.$fireDb.ref("clients/").push({
                    "owner": this.userId,
					"name" : this.client.name,
					"phone": (this.client.phone) ? this.client.phone : null,
                    "mail" : (this.client.mail) ? this.client.mail : null
				}).then(response => {
					this.creationActive = false
					this.creationDone = true
					setTimeout(() => {
						this.$router.push('/dashboard')
					}, 1000)
				}).catch(error => {
					this.creationActive = false
					this.errorMessage = error.message
				})
            }
		}
    },

	// Set calendar to french
	// Redirect to home if there is no logged in user
    mounted() {
        this.$fireAuth.onAuthStateChanged(user => {
            if(user) {
                this.userId = user.uid
            } else {
				this.$router.push('/')
			}
        })
    },
}
</script>