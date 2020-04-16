<template>
    <div class="quick_action">
        <div v-if="disabled">
            <div class="quick_action__elements disabled">
                <md-icon class="md-size-2x">{{ icon }}</md-icon>
                <h2>{{ text }}</h2>
            </div>
        </div>
        <div v-else>
            <nuxt-link :to="destination">
                <div class="quick_action__elements">
                    <md-icon class="md-size-2x">{{ icon }}</md-icon>
                    <h2>{{ text }}</h2>
                </div>
            </nuxt-link>
        </div>
    </div>
</template>

<script>
export default {
    name: "CreateItem",
    props: {
        icon: String, // Material Design Icon
        text: String, // Text displayed
        destination: String, // Link target
        disabled: Boolean // Enable or disable item creation
    }
}
</script>