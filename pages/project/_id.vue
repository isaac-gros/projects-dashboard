<template>
	<div class="container" id="project__view" :class="updateDone ? 'function_done' : ''">
		<div class="row">
			<div class="col-12">
				<h1 class="section_title">
					Modifier un projet
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
					<ProjectForm
						:object="project"
						@input="(update) => {project = update}"/>
					<div class="row">
						<div class="col-12">
							<div class="submit_buttons">
								<md-button class="md-raised" v-on:click="cancelProject">
									Annuler
								</md-button>
								<div class="submit_buttons__choices">
									<md-button 
										class="md-raised md-primary"
										:disabled="project.invalid"
										v-on:click="updateProject">
										Mettre à jour
									</md-button>
									<md-button
										class="md-raised"
										v-on:click="deleteProject"
										:disabled="deleteStepIndex == 2">
										{{ deleteStep[deleteStepIndex] }}
									</md-button>
								</div>
							</div>
						</div>
						<div class="col-12" v-if="errorMessage">
							<p>Erreur lors de la mise à jour du projet : {{ errorMessage }}</p>
						</div>
					</div>
				</div>
			</div>
		</div>
	</div>
</template>

<script>
import ProjectForm from "~/components/forms/ProjectForm.vue"

export default {
	name: "ProjectView",
	
	head() {
		return {
			titleTemplate: `Éditer le projet ${this.projectTitle}`,
			meta: [
				{ hid: 'description', name: 'description', content: `Mettre à jour le projet ${this.projectTitle}.` }
			]
		}
	},

    components: {
        ProjectForm
	},
	
	// Check if the id parameter has correct format 
	async validate({ params }) {
		return /^([A-Z]|[a-z]|(\-|\_)|[0-9]){20}$/.test(params.id)
	},

    data() {
        return {
			// Form fields
			userId: '',
			projectId: '',
			projectTitle: '', // Meta title
			project: {
				clients: [], // Already selected clients
				userClients: [], // All clients created
				userHasClients: false,
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
				"Supprimer le projet",
				"Confirmer la suppression",
				"Suppression..."
			]
        }
    },
    
    methods: {

        // Cancel project creation
        cancelProject() {
            this.$router.back()
		},

        // Update project to Firebase
        updateProject() {
            if(!this.project.invalid) {

				// Update component states
				this.deleteStepIndex = 0
				this.errorMessage = ''
				this.creationActive = true

				// Update project to Firebase
				this.$fireDb.ref("projects/" + this.projectId).update({
					"title" : this.project.title,
					"endDate" : (this.project.endDate) ? this.project.endDate : null,
					"status" : this.project.status,
					"clients": (this.project.clients) ? this.project.clients : null
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

		// Remove project from Firebase
		deleteProject() {
			this.deleteStepIndex++
			if(this.deleteStepIndex == 2) {
				
				// Disable project edition
				this.project.preventEdit = true
				this.updateActive = true

				// Delete project from Firebase
				this.$fireDb.ref("projects/" + this.projectId)
					.remove()
					.then(response => {
						this.updateActive = false
						this.updateDone = true
						this.$router.push('/dashboard')
					})

			}	
		},

		// Display project
		displayProject(project) {

			// Project data
			this.project.clients = project.clients
			this.project.title = project.title
			this.project.endDate = project.endDate
			this.project.status = project.status

			// Status of the form
			this.project.userHasClients = (this.project.userHasClients !== null)
			this.project.preventEdit = false
			this.updateActive = false
		},

		// Retrieve all clients created by user
		fetchUserClients() {
			this.$fireDb.ref("clients/")
				.orderByChild("owner")
				.equalTo(this.userId)
				.on('value', snapshot => {
					this.project.userClients = this.readUserClients(snapshot.val())
				})
		},

		// Read clients objects to display them
		readUserClients(clients) {

			// Final array where the data will be readable
			let readableData = []
			if(clients) {
				for(let [clientId, client] of Object.entries(clients)) {
					readableData.push({
						"id": clientId,
						"name": client.name,
					})
				}
			}
			
			return readableData
		},

        // Set calendar to french
        frenchLocale() {
            return {
                startYear: 1900,
                endYear: 2099,
                dateFormat: 'dd/MM/yyyy',
                days: ['Dimanche', 'Lundi', 'Mardi', 'Mercredi', 'Jeudi', 'Vendredi', 'Samedi'],
                shortDays: ['Dim', 'Lun', 'Mar', 'Mer', 'Jeudi', 'Ven', 'Sam'],
                shorterDays: ['D', 'L', 'Ma', 'Me', 'J', 'V', 'S'],
                months: ['Janvier', 'Février', 'Mars', 'Avril', 'Mai', 'Juin', 'Juillet', 'Août', 'Septembre', 'Octobre', 'Novembre', 'Décembre'],
                shortMonths: ['Jan', 'Fév', 'Mars', 'Avril', 'Mai', 'Juin', 'Juil', 'Août', 'Sept', 'Oct', 'Nov', 'Déc'],
                shorterMonths: ['J', 'F', 'M', 'A', 'M', 'Juin', 'Juil', 'A', 'Se', 'O', 'N', 'D'],
                firstDayOfAWeek: 1
            }
        }
    },

    mounted() {
        this.$material.locale = this.frenchLocale()
        this.$fireAuth.onAuthStateChanged(user => {
            if(user) {

				// Check if the user has right to update a project
				this.$fireDb.ref("projects/" + this.$route.params.id)
					.on('value', snapshot => {
						let project = snapshot.val()
						if(project.owner === user.uid) {
							this.userId = user.uid
							this.projectId = this.$route.params.id
							this.projectTitle = project.title
							this.fetchUserClients() // Get the user client
							this.displayProject(project) // Display and edit project
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