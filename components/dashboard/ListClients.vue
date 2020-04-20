<template>
    <div class="list_component">
        <h1 class="section_title">Liste des clients</h1>
        <div v-if="!noClients">
            <md-table v-model="clients" md-sort="asc">
                <md-table-row slot="md-table-row" slot-scope="{ item }">
                    <md-table-cell md-label="Nom" md-sort-by="name">
                        <nuxt-link :to="'client/' + item.id">
                            {{ item.name }}
                        </nuxt-link>
                    </md-table-cell>
                    <md-table-cell md-label="Téléphone" md-sort-by="phone" v-on:click="copyText(item.phone)">
						{{ item.phone }}
					</md-table-cell>
                    <md-table-cell md-label="Mail" md-sort-by="mail">
						{{ item.mail }}
						<span class="action" v-on:click="copyText(item.mail)">
							<md-icon>
								{{ copyDone ? 'done' : 'filter_none' }}
							</md-icon>
							<md-tooltip md-direction="right">
								{{ copyDone ? 'Copié !' : 'Copier l\'adresse' }}
							</md-tooltip>
						</span>
					</md-table-cell>
                </md-table-row>
            </md-table>
        </div>
        <div v-else>
            <p>
				Aucun client n'a été ajouté. 
				<nuxt-link to="/add/client/">Ajouter un client</nuxt-link>
			</p>
        </div>
    </div>
</template>

<script>
export default {
    name: "ListClients",
    data() {
        return {
			fetchActive: false,
			noClients: false,
			copyDone: false,

			// User's clients
            clients: []
        }
    },
    methods: {

        // Fetch user clients
        fetchUserClients(userId) {
			this.$fireDb.ref("clients/")
				.orderByChild("owner")
				.equalTo(userId)
				.on('value', snapshot => {
					if(snapshot.val()) {
						this.clients = this.readClientsData(snapshot.val())
						this.fetchActive = false
					} else {
						this.fetchActive = false
						this.noClients = true
					}
				})
        },
        
        // Transform clients objects to render them
        readClientsData(clients) {

			// Final array where the data will be readable
			let readableData = []

			for(let [clientId, client] of Object.entries(clients)) {

				let clientName = client.name
				let clientMail = (client.mail) ? client.mail : "Non renseignée"
				let clientPhone = (client.phone) ? client.phone : "Non renseigné"
				readableData.push({
					"id": clientId,
					"name": clientName,
					"mail": clientMail,
					"phone": clientPhone
				})
			}

			return readableData
		},

		async copyText(text) {
			try {
				await this.$copyText(text)
				this.copyDone = true
				setTimeout(() => {
					this.copyDone = false
				}, 400)
			} catch (e) {
				console.error(e)
			}
		}
	},

	// Get clients
    mounted() {
        this.$fireAuth.onAuthStateChanged(user => {
			if(user) {
				this.fetchActive = true
				this.fetchUserClients(user.uid)
			}
        })
    }
}
</script>