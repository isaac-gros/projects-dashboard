<template>
	<div id="home" class="function_active" v-if="noUserLoggedIn">
		<div class="forms_container">
			<div class="container">
				<div class="row">
					<div class="col-lg-4 col-md-12 offset-lg-1 align-center">
						<LoginForm/>
					</div>
					<div class="col-lg-2 col-md-12 align-center">
						<span class="forms_container__separator"></span>
					</div>
					<div class="col-lg-4 col-md-12 align-center">
						<RegisterForm/>
					</div>
				</div>
			</div>
		</div>
	</div>
	<div v-else>
		<div class="spinner_container">
			<div class="spinner_container__element">
				<md-progress-spinner 
					class="md-primary"
					md-mode="indeterminate"
					md-diameter=40
					md-stroke=4>
				</md-progress-spinner>
			</div>
			<p>Vérification...</p>
		</div>
	</div>
</template>

<script>
import LoginForm from "~/components/auth/LoginForm.vue";
import RegisterForm from "~/components/auth/RegisterForm.vue";

export default {
	components: {
		LoginForm,
		RegisterForm
	},
	data() {
		return {
			noUserLoggedIn: false
		}
	},
	mounted() {
		this.$fireAuth.onAuthStateChanged(user => {
			if(user) {
				this.$router.push('/dashboard')
			} else {
				this.noUserLoggedIn = true
			}
		})
	}
}
</script>