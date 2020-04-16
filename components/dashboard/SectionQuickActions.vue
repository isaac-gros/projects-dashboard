<template>
    <div class="container" id="quick-actions">
        <div class="row">
            <div class="col-12">
                <h1 class="section_title">Actions rapides</h1>
            </div>
             <div class="col-lg-3 col-md-12">
                <CreateItem
                    text="Nouveau projet" 
                    icon="work"
                    destination="/add/project"/>
            </div>
            <div class="col-lg-3 col-md-12">
                <CreateItem
                    text="Nouveau client" 
                    icon="person_add"
                    destination="/add/client"/>
            </div>
            <div class="col-lg-3 col-md-12">
                <CreateItem
                    text="Nouveau devis" 
                    icon="receipt"
                    destination="#"
                    disabled/>
            </div>
            <div class="col-lg-3 col-md-12">
                <CreateItem
                    text="Relevés" 
                    icon="account_balance_wallet"
                    destination="#"
                    disabled/>
            </div>
        </div>
    </div>
</template>

<script>
import CreateItem from "~/components/dashboard/CreateItem"

export default {
    name: "SectionQuickActions",
    components: {
        CreateItem
    }
}
</script>