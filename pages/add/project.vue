<template>
	<div class="container" id="project__new" :class="creationDone ? 'function_done' : ''">
		<div class="row">
			<div class="col-12">
				<h1 class="section_title">
					Ajouter un nouveau projet
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
					<ProjectForm
						:object="project"
						@input="(update) => {project = update}"/>
					<div class="row">
						<div class="col-12">
							<div class="submit_buttons">
								<md-button class="md-raised" v-on:click="cancelProject">
									Annuler
								</md-button>
								<md-button 
									class="md-raised md-primary"
									:disabled="project.invalid" 
									v-on:click="submitProject">
									Créer le projet
								</md-button>
							</div>
						</div>
						<div class="col-12" v-if="errorMessage">
							<p>Erreur lors de la création du projet : {{ errorMessage }}</p>
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
    name: "ProjectAdd",

    components: {
        ProjectForm
    },

    data() {
        return {
			// Form default fields
			userId: '',
            project: {
				status: "todo",
				userClients: [],
				userHasClients: false,
				invalid: null
			},
			
			// States of the component
			creationActive: false,
			creationDone: false,
			errorMessage: ''
        }
    },
    
    methods: {

        // Cancel project creation
        cancelProject() {
            this.$router.back()
        },

        // Submit project to Firebase
        submitProject() {
            if(!this.project.invalid) {
				this.errorMessage = ''
				this.creationActive = true

				this.$fireDb.ref("projects/").push({
                    "owner": this.userId,
					"title" : this.project.title,
					"created": this.$moment().toDate().getTime(),
                    "endDate" : (this.project.endDate) ? this.$moment(this.project.endDate).format("L") : null,
                    "status" : this.project.status,
                    "clients": (this.project.clients) ? this.project.clients : null
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
		},

		// Get user's client from Firebase
		getUserClients() {
			this.$fireDb.ref("clients/")
				.orderByChild("owner")
				.equalTo(this.userId)
				.on('value', snapshot => {
					let clients = snapshot.val()
					this.project.userClients = this.readUserClients(clients)
					this.project.userHasClients = (this.project.userClients !== null)
				})
		},

		// Display clients in front-end
		readUserClients(clients) {

			// Final array where the data will be readable
			let readableData = []
			if(clients) {
				this.userHasClients = true
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

	// Set calendar to french
	// Redirect to home if there is no logged in user
    mounted() {
        this.$material.locale = this.frenchLocale();
        this.$fireAuth.onAuthStateChanged(user => {
            if(user) {
				this.userId = user.uid
				this.getUserClients()
            } else {
				this.$router.push('/')
			}
        })
    },
}
</script>