<template>
    <div class="menu">
        <div class="menu__container">
            <md-button class="md-icon-button" v-on:click="toggleMenu">
                <md-icon>menu</md-icon>
            </md-button>
        </div>
        <md-drawer :md-active.sync="showNavigation" class="md-right menu__drawer">
            <md-toolbar class="md-transparent" md-elevation="0">
                <span class="md-title">Mes projets</span>
                <md-progress-spinner 
                    class="md-primary"
                    md-mode="indeterminate"
                    md-diameter=25
                    md-stroke=3
                    v-if="logoutProcessActive">
                </md-progress-spinner>
            </md-toolbar>
            
            <md-list>
                <nuxt-link to="/dashboard" v-on:click.native="toggleMenu">
                    <md-list-item>
                        <md-icon>dashboard</md-icon>
                        <span class="md-list-item-text">Tableau de bord</span>
                        <md-tooltip md-direction="left">Vue d'ensemble des projets</md-tooltip>
                    </md-list-item>
                </nuxt-link>
               <nuxt-link to="/add/project/" v-on:click.native="toggleMenu">
                    <md-list-item>
                        <md-icon>work</md-icon>
                        <span class="md-list-item-text">Nouveau projet</span>
                        <md-tooltip md-direction="left">Ajouter un projet</md-tooltip>
                    </md-list-item>
               </nuxt-link>
                <nuxt-link to="/add/client" v-on:click.native="toggleMenu">
                    <md-list-item>
                        <md-icon>person_add</md-icon>
                        <span class="md-list-item-text">Nouveau client</span>
                        <md-tooltip md-direction="left">Ajouter un client</md-tooltip>
                    </md-list-item>
                </nuxt-link>
            </md-list>

            <md-divider></md-divider>

            <md-list>
                <md-list-item v-on:click="userLogout">
                    <md-icon class="text-danger">power_settings_new</md-icon>
                    <span class="md-list-item-text text-danger">Déconnexion</span>
                    <md-tooltip md-direction="left">Ce n'est qu'un au revoir !</md-tooltip>
                </md-list-item>
                <a v-on:click="toggleMenu" href="https://github.com/isaac-gros/dvic-monitoring" rel="noopener" target="_blank">
                    <md-list-item>
                        <md-icon>star</md-icon>
                        <span class="md-list-item-text">À propos de cet outil</span>
                        <md-tooltip md-direction="left">Consulter le répertoire Github du projet</md-tooltip>
                    </md-list-item>
                </a>
            </md-list>

            <div class="menu__legals">
                <p class="md-primary">
                    <a href="https://isaacgros.fr/" rel="noopener" target="_blank">Isaac Gros</a> — 2020
                </p>
            </div>
            
        </md-drawer>
    </div>
</template>

<script>
export default {
    name: "Menu",
    data() {
        return {
            showNavigation: false,
        }
    },
    methods: {
        // Toggle menu
        toggleMenu() {
            this.showNavigation = !this.showNavigation
        },

        // Logout user
        userLogout() {
            this.logoutProcessActive = true
            this.$fireAuth.signOut().then(
                this.$router.push('/')
            )
        }
    }
}
</script>