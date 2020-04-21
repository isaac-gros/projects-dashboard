<template>
    <div class="list_component">
        <h1 class="section_title">
			Liste des projets
			<md-progress-spinner 
				class="md-primary"
				md-mode="indeterminate"
				md-diameter=25
				md-stroke=3
				v-if="fetchActive">
			</md-progress-spinner>
		</h1>
		<div v-if="!noProjects">
			<md-table v-model="projects" md-sort="asc">
				<md-table-row slot="md-table-row" slot-scope="{ item }">
					<md-table-cell md-label="Titre" md-sort-by="title">
						<nuxt-link :to="'project/' + item.id">
							{{ item.title }}
						</nuxt-link>
					</md-table-cell>
					<md-table-cell md-label="Client(s)" md-sort-by="clients" v-html="item.clients"></md-table-cell>
					<md-table-cell md-label="Statut" md-sort-by="status">{{ item.status }}</md-table-cell>
					<md-table-cell md-label="Échéance" md-sort-by="endDate" class="md-primary">{{ item.endDate }}</md-table-cell>
				</md-table-row>
			</md-table>
		</div>
		<div v-else>
			<p>
				Aucun projet n'a été créé. 
				<nuxt-link to="/add/project">Créez un projet</nuxt-link>
			</p>
		</div>
    </div>
</template>

<script>
export default {
    name: "ListProjects",
    data() {
        return {
			fetchActive: false,
			noProjects: false,

			// User's projects
            projects: []
        }
    },
    methods: {

        // Fetch user projects
        fetchUserProjects(userId) {
			this.$fireDb.ref("projects/")
				.orderByChild("owner")
				.equalTo(userId)
				.on('value', snapshot => {
					if(snapshot.val()) {
						let projectsArray = snapshot.val()
						this.projects = this.readProjectsData(projectsArray)
						this.fetchActive = false
					} else {
						this.fetchActive = false
						this.noProjects = true
					}
				})
        },
        
        // Transform project objects to render them
        readProjectsData(projects) {

			// "Translate" status
			let statusTranslations = {
				"todo": {
					"value": "À faire"
				},
				"work-in-progress": {
					"value": "En cours"
				},
				"done": {
					"value": "Terminé"
				},
				"need-fix": {
					"value": "Corrections demandées"
				}
			}

			// Final array where the data will be readable
			let readableData = []

			for(let [projectId, project] of Object.entries(projects)) {
				let projectStatus = project.status
				let projectEndDate = (typeof(project.endDate) !== "undefined") ? project.endDate : "Indéfinie"
				let projectClients = this.readProjectClientsData(project.clients)
				readableData.push({
					"id": projectId,
					"title": project.title,
					"endDate": projectEndDate,
					"clients": projectClients,
					"status": statusTranslations[projectStatus].value
				})
			}

			return readableData
		},

		// Turns clients IDs to their real name
		readProjectClientsData(clients) {
			let clientsString = ""
			let clientIndex = 1
			if(typeof(clients) !== "undefined") {
				for(let clientId of clients) {
					
					// Retrieve the client by it ID
					this.$fireDb.ref("clients/" + clientId + "/name")
						.on('value', snapshot => {
							clientsString += snapshot.val()
						})

					// Display settings
					if(clients.length > 1) {
						clientsString += (clientIndex == clients.length) ? '' : ' — <br>'
					}
					clientIndex++
				}
			} else {
				clientsString = "Aucun client"
			}

			return clientsString
		}
	},

	// Get projects
    mounted() {
        this.$fireAuth.onAuthStateChanged(user => {
			if(user) {
				this.fetchActive = true
				this.fetchUserProjects(user.uid)
			}
        })
    }
}
</script>