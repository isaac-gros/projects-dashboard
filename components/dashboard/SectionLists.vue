<template>
    <div class="container" id="lists">
        <div class="row">
            <div class="col-lg-6 col-md-12">
                <ListProjects 
                    listTitle="Aperçu des projets"
                    :items="projects"/>
            </div>
            <div class="col-lg-6 col-md-12">
                <ListClients
                    listTitle="Liste des clients"
                    :items="clients"/>
            </div>
        </div>
    </div>
</template>

<script>
import ListProjects from "~/components/dashboard/ListProjects"
import ListClients from "~/components/dashboard/ListClients"

export default {
    name: "SectionLists",
    components: {
        ListProjects,
        ListClients
    },
    data() {
        return {
            "projects": [],
            "clients": [],
        }
    }
}
</script>