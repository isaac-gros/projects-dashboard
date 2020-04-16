<template>
    <div class="list_component">
        <h1 class="section_title">Liste des clients</h1>
        <md-table v-model="items" md-sort="asc">
            <md-table-row slot="md-table-row" slot-scope="{ item }">
                <md-table-cell md-label="Nom" md-sort-by="name">{{ item.name }}</md-table-cell>
                <md-table-cell md-label="Téléphone" md-sort-by="phone">{{ item.phone }}</md-table-cell>
                <md-table-cell md-label="Mail" md-sort-by="mail">{{ item.mail }}</md-table-cell>
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