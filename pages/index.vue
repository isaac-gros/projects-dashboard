<template>
	<div id="home">
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
			authenticatedUser: false
		}
	},
	update() {
		this.$fireAuth.onAuthStateChanged(user => {
			if(user && !authenticatedUser) {
				this.authenticatedUser = true
				console.log(user);
			}
		})
	}
}
</script>