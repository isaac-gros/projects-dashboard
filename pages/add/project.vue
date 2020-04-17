<template>
	<div class="container" id="project__new">
		<div class="row">
			<div class="col-12">
				<h1 class="section_title">Ajouter un nouveau projet</h1>
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
            userId: '',
            project: {
                status: "todo",
                invalid: true
            }
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
                console.log({
                    "owner": this.userId,
                    "title" : this.project.title,
                    "endDate" : this.project.endDate,
                    "status" : this.project.status,
                    "clients": this.project.clients
                })
            }
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
            } else {
				this.$router.push('/')
			}
        })
    },
}
</script>