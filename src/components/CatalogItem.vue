<template>
    <article class="catalog-item-container">
        <h2 class="catalog-item-header">
            <img src="#" :alt="catalogItem.carrier.caption">
            <div class="price-container">
                <p class="price">{{ getPrice }}</p>
                <p class="price-info">Стоимость для одного взрослого пасссажира</p>
            </div>
        </h2>
        <Flight v-for="(flight, index) in catalogItem.legs" :key="index" :flight="flight"/>
        <Button class="catalog-item-button">Выбрать</Button>
    </article>
</template>

<script lang="ts">
    import type { PropType } from "vue";
    import type { Flight as FlightType } from "@/types";
    import Flight from "@/components/Flight.vue";
    
    export default {
        props: {
            catalogItem: {
                type: Object as PropType<FlightType>,
                required: true
            }
        },
        computed: {
            getPrice() {
                return new Intl.NumberFormat("ru", {
                    style: "currency",
                    currency: this.catalogItem.price.passengerPrices[0].total.currencyCode,
                    maximumFractionDigits: 0
                }).format(Number(this.catalogItem.price.passengerPrices[0].total.amount));
            },
        },
        components: { Flight },
    }
</script>

<style scoped>
    .catalog-item-container {
        width: 100%;
        background-color: var(--blue-color);
        display: flex;
        flex-direction: column;
        gap: 2px;
        margin-bottom: 20px;

        .catalog-item-header {
            width: 100%;
            color: var(--white-color);
            display: flex;
            justify-content: space-between;
            align-items: center;
            padding: 0 20px;

            .price-container {      
                .price {
                    text-align: end;
                }

                .price-info {
                    font-size: 1rem;
                }
            }

            @media (max-width: 568px) {
                flex-direction: column;

                .price-container {
                    display: flex;
                    flex-direction: row-reverse;
                    align-items: center;
                    gap: 10px;
                }
            }
        }

        .catalog-item-button {
            text-transform: uppercase;
            background-color: var(--orange-color);
            color: var(--white-color);
            border: none;
            padding: 10px 15px;

            @media (hover: hover) {
                &:hover {
                    background-color: var(--light-orange-color);
                    
                    
                }
            }
        }
    }
</style>
