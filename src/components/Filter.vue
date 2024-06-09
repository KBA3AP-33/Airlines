<template>
    <aside class="filter-container">
        <span class="filter-close-button" @click="$emit('openCloseFilter', false)">&#x2715;</span>
        <ul class="filter-list" v-for="filter in getFilters" :key="filter.key">
            <li class="filter-list-item">
                <h3 class="filter-title">{{ filter.name }}</h3>
                <component
                    :is="filter.type"
                    :items="filter.points"
                    :group="filter.key"
                    @changeValue="changeFilter"/>
            </li>
        </ul>
    </aside>
</template>

<script lang="ts">
    import type { Flight } from '@/types';
    import type { PropType } from 'vue';
    import settings from "@/settings/index.json";

    export default {
        data: () => ({
            currentFilters: [] as Array<{ key: string, values: Array<number | string> }>,
            filterItems: [] as Array<Flight>,
            filters: settings.filter,
        }),
        props: {
            items: {
                type: Array as PropType<Array<Flight>>,
                required: true
            }
        },
        mounted() {
            this.filterItems = this.items;
        },
        methods: {
            sort(items: Array<Flight>, value: number) {
                switch (value) {
                    case 1:
                        return items.sort((a, b) => (Number(a.price.total.amount) - Number(b.price.total.amount)) * 1);
                    case 2:
                        return items.sort((a, b) => (Number(a.price.total.amount) - Number(b.price.total.amount)) * -1);
                    case 3:
                        return items.sort((a, b) => 
                            (Number(a.legs.reduce((acc, el) => (acc.duration + el.duration) as any)) - Number(b.legs.reduce((acc, el) => (acc.duration + el.duration as any)))));
                }
            },
            changeFilter(filter: { key: string, values: Array<number | string> }) {
                this.currentFilters = [...this.currentFilters.filter(e => e.key !== filter.key), filter].filter(e => e.values.length);
                let result = this.currentFilters
                    .map(e => (e.key === 'filter')
                        ? ((x: Flight) => e.values.length ? e.values.some(i => x.legs.map(x => x.segments.length - 1).includes(Number(i))) : true)
                        : (e.key === 'price')
                            ? (x: Flight) => Number(x.price.total.amount) >= Number(e.values[0]) && Number(x.price.total.amount) <= Number(e.values[1])
                            : (e.key === 'conpany')
                                ? (x: Flight) => this.currentFilters.find(i => i.key === e.key)?.values?.includes(x.carrier.caption)
                                : () => true)
                    .reduce((d, f) => d.filter(f) , this.items);

                let sort = this.currentFilters.find(e => e.key === 'sort');
                this.filterItems = ((typeof(sort) !== 'undefined') ? this.sort(result, Number(sort.values[0])) : result) as Array<Flight>;
            },
        },
        computed: {
            getFilters() {
                let filter = {
                    ...this.filters[1],
                    points: Array.from(new Set(this.items
                        .flatMap(e => e.legs.map(e => e.segments.length - 1))))
                        .map(e => ({
                            text: (e === 0) ? 'без пересадок' : `${e} пересадка`,
                            value: e,
                            disabled: !this.filterItems.flatMap(e => e.legs.map(e => e.segments.length - 1)).includes(Number(e)),
                        })),
                };

                let company = {
                    ...this.filters[3],
                    points: Array.from(new Set(this.items
                        .flatMap(e => e.carrier.caption)))
                        .map(e => {

                            let currency = this.filterItems.find(i => i.carrier.caption === e)?.price.total.currencyCode;
                            let min = Math.min(...this.filterItems.filter(i => i.carrier.caption === e).map(i => Number(i.price.total.amount)));
                            let text = `${min !== Number.POSITIVE_INFINITY ? ` (от ${
                                new Intl.NumberFormat("ru", {
                                    style: "currency",
                                    currency: (currency) ? currency : 'RUB',
                                    maximumFractionDigits: 0
                                }).format(Number(min))})` : ''}`;

                            return {
                                text: `${e}${text}`,
                                value: e,
                                disabled: !this.filterItems.map(e => e.carrier.caption).includes(e),
                            }
                        }),
                };

                return [ this.filters[0], filter, this.filters[2], company ];
            },
        },
        watch: {
            filterItems(nextValue) {
                this.$emit('changeFilter', nextValue);
            }
        },
    }
</script>

<style scoped>
    .filter-container {
        background-color: var(--white-color);
        padding: 5px 20px;
        position: relative;
        transition: all .5s linear;

        &.open {
            transform: translateX(100%);
        }
 
        .filter-close-button {
            font-size: 1.2rem;
            position: absolute;
            top: 0;
            right: 2px;
            display: none;
            cursor: pointer;

            @media(hover: hover) {
                &:hover {
                    color: var(--blue-color);
                }
            }

            @media (max-width: 992px) {
                display: block;
            }
        }


        .filter-list {
            list-style: none;

            .filter-list-item {
                margin-bottom: 20px;

                .filter-title {
                    margin-bottom: 10px;
                }
            }
        }

        @media (max-width: 992px) {
            height: 100%;
            position: absolute;
            top: 0;
            left: -320px;
            z-index: 100000;
        }
    }
</style>
