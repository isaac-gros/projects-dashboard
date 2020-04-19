<template>
    <div class="container">
		<div class="row">
			<div class="col-12">
				<div class="error_page">
					<div class="error_page__message">
						
						<md-icon class="md-size-3x md-primary" v-if="error.statusCode == 404">help_outline</md-icon>
						<md-icon class="md-size-3x md-primary" v-else-if="error.statusCode == 403">cancel</md-icon>
						<md-icon class="md-size-3x md-primary" v-else>sentiment_very_dissatisfied</md-icon>

						<h1 v-if="error.statusCode == 404">Page introuvable</h1>
						<h1 v-else-if="error.statusCode == 403">Accès interdit</h1>
						<h1 v-else>Une erreur est survenue</h1>

						<nuxt-link to="/dashboard" class="md-button md-raised md-theme-default md-primary">
							<div class="md-ripple">
								<div class="md-button-content">
									Retourner au tableau de bord
								</div>
							</div>
						</nuxt-link>
					</div>
				</div>
			</div>
		</div>
        
    </div>
</template>

<script>
export default {
	layout: 'default',
	props: ['error']
}
</script>