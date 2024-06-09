<template>
    <section class="catalog-container">
        <div class="mobile-buttons">
            <Button @click="$emit('openCloseFilter')">Фильтр</Button>
        </div>
        <TransitionGroup name="list">
            <CatalogItem v-for="(item, index) in getCatalogItems" :key="index" :catalog-item="item"/>
        </TransitionGroup>
        <p v-if="!getCatalogItems.length">Список пуст!</p>
        <Button v-if="hasNextItems" @click="page++">Показать еще</Button>
    </section>
</template>

<script lang="ts">
    import settings from "@/settings/index.json";
    import CatalogItem from "@/components/CatalogItem.vue";
    import type { Flight } from "@/types";
    import type { PropType } from "vue";
    
    export default {
        data: () => ({
            hasNextItems: true,
            page: 1,
        }),
        props: {
            catalogItems: {
                type: Array as PropType<Array<Flight>>, 
                required: true
            }
        },
        computed: {
            getCatalogItems() {
                let end = this.page * settings.catalog.count;
                this.hasNextItems = this.catalogItems.length > end;
                return this.catalogItems.slice(0, end);
            },
        },
        watch: {
            catalogItems: {
                handler() {
                    this.page = 1;  
                },
                deep: true,
            },
        },
        components: { CatalogItem }
    }
</script>

<style scoped>
    .catalog-container {
        width: 100%;
        max-width: 1200px;
        display: flex;
        flex-direction: column;
        justify-content: flex-start;
        align-items: center;

        .mobile-buttons {
            width: 100%;
            margin-bottom: 10px;
            display: none;
            
            @media (max-width: 992px) {
                display: flex;
                justify-content: flex-end;
            }
        }

        .list-move,
        .list-enter-active,
        .list-leave-active {
            transition: all .5s ease-in-out;
        }

        .list-enter-from,
        .list-leave-to {
            opacity: 0;
            transform: translateX(30px);
        }

        .list-leave-active {
            position: absolute;
        }
    }
</style>
