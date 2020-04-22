<template>
	<div id="app">
		<Menu v-if="($route.path !== '/') && ($route.name !== null)"/>
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