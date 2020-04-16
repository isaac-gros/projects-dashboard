<template>
	<div id="dashboard">
		<SectionQuickActions/>
		<SectionLists/>
	</div>
</template>

<script>
import SectionQuickActions from "~/components/dashboard/SectionQuickActions"
import SectionLists from "~/components/dashboard/SectionLists"

export default {
	components: {
		SectionQuickActions,
		SectionLists
	},
	data() {
		return {
			userProjects: [],
			userClients: []
		}
	},
	methods: {
		
		// Fill the page with the user's data
		fetchUserData() {
			this.$fireAuth.onAuthStateChanged(user => {
				if(user) {
					let userId = user.uid
					let userProjects = this.fetchUserProjects(userId)
					let userClients = this.fetchUserClients(userId)

				} else {
					this.$router.push("/")
				}
			})
		},

		fetchUserProjects(userId) {
			// TODO : Fetch user projects
		},
		
		fetchUserClients(userId) {
			// TODO : Fetch user clients
		}
	},
	created() {
		this.fetchUserData()
	}
}
</script>