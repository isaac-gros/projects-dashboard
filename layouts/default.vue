<template>
	<div id="app">
		<Menu v-if="$route.path !== '/'"/>
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