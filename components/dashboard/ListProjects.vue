<template>
    <div class="list_component">
        <h1 class="section_title">Liste des projets</h1>
        <md-table v-model="items" md-sort="asc">
            <md-table-row slot="md-table-row" slot-scope="{ item }">
                <md-table-cell md-label="Titre" md-sort-by="title">{{ item.title }}</md-table-cell>
                <md-table-cell md-label="Création" md-sort-by="startDate">{{ item.startDate }}</md-table-cell>
                <md-table-cell md-label="Échéance" md-sort-by="endDate">{{ item.endDate }}</md-table-cell>
                <md-table-cell md-label="Statut" md-sort-by="status">{{ item.status }}</md-table-cell>
            </md-table-row>
        </md-table>
    </div>
</template>

<script>
export default {
    name: "ListProjects",
    props: {
        items: Array
    }
}
</script>