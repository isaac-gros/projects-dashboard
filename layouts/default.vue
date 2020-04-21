<template>
	<div id="app">
		<Menu v-if="($route.path !== '/') && ($nuxt.error == null)"/>
		<nuxt/>
	</div>
</template>

<script>
import Menu from "~/components/dashboard/Menu"

export default {
	components: {
		Menu
	}
}
</script>