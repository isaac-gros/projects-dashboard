<template>
	<div id="dashboard">
		<SectionQuickActions/>
		<div class="container" id="lists">
			<div class="row">
				<div class="col-lg-6 col-md-12">
					<ListProjects/>
				</div>
				<div class="col-lg-6 col-md-12">
					<ListClients/>
				</div>
			</div>
		</div>
	</div>
</template>

<script>
import SectionQuickActions from "~/components/dashboard/SectionQuickActions"
import ListProjects from "~/components/dashboard/ListProjects"
import ListClients from "~/components/dashboard/ListClients"

export default {
	name: "Dashboard",

	head: {
		titleTemplate: "Mes projets - Tableau de bord",
		meta: [
			{ hid: 'description', name: 'description', content: 'Consulter et gérez vos clients et projets.' }
		]
	},

	components: {
		SectionQuickActions,
		ListProjects,
		ListClients
	},

	// Redirect user to login page if not logged in
	mounted() {
		this.$fireAuth.onAuthStateChanged(user => {
			if(!user) {
				this.$router.push("/")
			}
		})
	}
}
</script>