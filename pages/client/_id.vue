<template>
	<div class="container" id="client__view" :class="updateDone ? 'function_done' : ''">
		<div class="row">
			<div class="col-12">
				<h1 class="section_title">
					Modifier un client
					<md-progress-spinner 
						class="md-primary"
						md-mode="indeterminate"
						md-diameter=25
						md-stroke=3
						v-if="updateActive">
					</md-progress-spinner>
					<md-icon
						v-if="updateDone"
						class="md-primary section_title__icon">done</md-icon>
				</h1>
			</div>
			<div class="col-12" v-if="deleteStepIndex == 1">
				<div class="alert alert-danger" role="alert">
					La suppression est irréversible. Confirmer ?
				</div>
			</div>
			<div class="col-12">
				<div class="container">
					<ClientForm
						:object="client"
						@input="(update) => {client = update}"/>
					<div class="row">
						<div class="col-12">
							<div class="submit_buttons">
								<md-button class="md-raised" v-on:click="cancelClient">
									Annuler
								</md-button>
								<div class="submit_buttons__choices">
									<md-button 
										class="md-raised md-primary"
										:disabled="client.invalid"
										v-on:click="updateClient">
										Mettre à jour
									</md-button>
									<md-button
										class="md-raised"
										v-on:click="deleteClient"
										:disabled="deleteStepIndex == 2">
										{{ deleteStep[deleteStepIndex] }}
									</md-button>
								</div>
							</div>
						</div>
						<div class="col-12" v-if="errorMessage">
							<p>Erreur lors de la mise à jour du client : {{ errorMessage }}</p>
						</div>
					</div>
				</div>
			</div>
		</div>
	</div>
</template>

<script>
import ClientForm from "~/components/forms/ClientForm"

export default {
	name: "ClientView",

	components: {
		ClientForm
	},
	
	// Check if the id parameter has correct format 
	async validate({ params }) {
		return /^([A-Z]|[a-z]|(\-|\_)|[0-9]){20}$/.test(params.id)
	},

	data() {
        return {
			// Form fields
			clientId: '',
			client: {
				preventEdit: true,
				invalid: false
			},
			
			// States of the component
			updateActive: true,
			updateDone: false,
			errorMessage: '',

			// Confirm deletion
			deleteStepIndex: 0,
			deleteStep: [
				"Supprimer le client",
				"Confirmer la suppression",
				"Suppression..."
			]
        }
	},
	
	methods: {

        // Cancel client creation
        cancelClient() {
            this.$router.back()
		},

        // Update client to Firebase
        updateClient() {
            if(!this.client.invalid) {
				this.deleteStepIndex = 0
				this.errorMessage = ''
				this.creationActive = true

				this.$fireDb.ref("clients/" + this.clientId).update({
					"name" : this.client.name,
					"phone" : (this.client.phone) ? this.client.phone : null,
					"mail" : (this.client.mail) ? this.client.mail : null,
				}).then(response => {
					this.updateActive = false
					this.updateDone = true
					setTimeout(() => {
						this.$router.push('/dashboard')
					}, 1000)
				}).catch(error => {
					this.updateActive = false
					this.errorMessage = error.message
				})
			}
		},

		deleteClient() {
			this.deleteStepIndex++
			if(this.deleteStepIndex == 2) {
				
				// Disable client edition
				this.client.preventEdit = true
				this.updateActive = true

				// Delete client from Firebase
				this.$fireDb.ref("clients/" + this.clientId)
					.remove()
					.then(response => {
						this.updateActive = false
						this.updateDone = true
						this.$router.push('/dashboard')
					})

			}	
		},

		// Display client
		displayClient(client) {
			this.client.name = client.name
			this.client.phone = client.phone
			this.client.mail = client.mail
			this.client.preventEdit = false
			this.updateActive = false
		},
	},
	
	mounted() {
        this.$fireAuth.onAuthStateChanged(user => {
            if(user) {

				// Check if the user has right to update the client
				this.$fireDb.ref("clients/" + this.$route.params.id)
					.on('value', snapshot => {
						let client = snapshot.val()
						if(client.owner === user.uid) {
							this.clientId = this.$route.params.id
							this.displayClient(client) // Display and edit client
						} else {
							this.$nuxt.error({ statusCode: 403 }) // Redirect user to a 403 error page
						}
					})
            } else {
				this.$router.push('/')
			}
		})
	}

}
</script>