<template>
    <div class="flight-container">
        <h3 class="flight-title">
            <span>{{ getDepartureCityAndAirport.city }}, {{ getDepartureCityAndAirport.airport.caption }}</span>
            <span class="flight-link"> ({{ getDepartureCityAndAirport.airport.uid }}) &rarr; </span>
            <span>{{ getArrivalCityAndAirport.city }}, {{ getArrivalCityAndAirport.airport.caption }}</span>
            <span class="flight-link"> ({{ getArrivalCityAndAirport.airport.uid }})</span>
        </h3>
        <div class="time-container">
            <p class="start-time">
                <span class="time">{{ getDepartureDate }}</span>
                <span class="date">{{ getDepartureDay }}</span>
            </p>
            <p class="time">
                <span style="font-size: 18px;">&#128339;</span>
                {{ getDurationFlight }}
            </p>
            <p class="end-time">
                <span class="date">{{ getArrivalDay }}</span>
                <span class="time">{{ getArrivalDate }}</span>
            </p>
        </div>
        <span class="transfer-count">
            <span v-if="flight.segments.length - 1">{{ flight.segments.length - 1 }} пересадка</span>
        </span>
        <p class="flight-company">
            Рейс выполняет: {{ (flight.segments[0].operatingAirline) ? flight.segments[0].operatingAirline.caption : flight.segments[0].airline.caption }}
        </p>
    </div>
</template>

<script lang="ts">
    import type { Segment } from '@/types';
    import type { PropType } from 'vue';

    export default {
        props: {
            flight: {
                type: Object as PropType<{ duration: number, segments: Array<Segment> }>,
                required: true
            },
        },
        methods: {
            getTime(date: string) {
                return new Intl.DateTimeFormat("ru", { timeStyle: "short" }).format(new Date(date));
            },
            getDate(date: string) {
                return new Intl.DateTimeFormat("ru", { day: 'numeric', month: 'short', weekday: 'short' })
                    .format(new Date(date))
                    .split(', ')
                    .reverse()
                    .join(' ');
            }
        },
        computed: {
            getDurationFlight() {
                return `${Math.floor(this.flight.duration / 60)} ч ${this.flight.duration % 60} мин`;
            },
            getDepartureDate() {
                return this.getTime(this.flight.segments[0].departureDate);
            },
            getDepartureDay() {
                return this.getDate(this.flight.segments[0].departureDate);
            },
            getArrivalDate() {
                return this.getTime(this.flight.segments[this.flight.segments.length - 1].arrivalDate);
            },
            getArrivalDay() {
                return this.getDate(this.flight.segments[this.flight.segments.length - 1].arrivalDate);
            },
            getDepartureCityAndAirport() {
                return {
                    city: this.flight.segments[0].departureCity?.caption,//
                    airport: this.flight.segments[0].departureAirport,
                };
            },
            getArrivalCityAndAirport() {
                return {
                    city: this.flight.segments[this.flight.segments.length - 1].arrivalCity?.caption,//
                    airport: this.flight.segments[this.flight.segments.length - 1].arrivalAirport,
                };
            },
        },
    }
</script>

<style scoped>
    .flight-container {
        width: 100%;
        background-color: var(--white-color);
        border-radius: 2px;
        display: flex;
        flex-direction: column;
        align-items: stretch;
        padding: 5px 10px;

        .flight-title {
            font-size: 1.25rem;
            font-weight: normal;
            border-bottom: 1px solid var(--ligth-gray-color);

            .flight-link {
                color: var(--blue-color);
            }
        }

        .time-container {
            display: flex;
            justify-content: space-between;
            align-items: center;

            .time {
                font-size: 1.25rem;
            }

            .start-time,
            .end-time {
                display: flex;
                align-items: baseline;
                gap: .5rem;

                .date {
                    color: var(--blue-color);
                }

                @media (max-width: 425px) {
                    flex-direction: column;
                    align-items: center;
                    gap: 0;
                }
            }

            .end-time {
                @media (max-width: 425px) {
                    flex-direction: column-reverse;
                    gap: 0;
                }
            }
        }

        .transfer-count {
            min-height: 1.5rem;
            text-align: center;
            text-wrap: nowrap;
            display: flex;
            justify-content: center;
            align-items: center;
            color: var(--orange-color);
            
            &::before,
            &::after {
                content: "";
                width: 100%;
                border-top: 1px solid var(--gray-color);
                margin: 0 15px;
                transform: translateY(2px);
            }

            &:not(:has(*))::before {
                margin-right: 0;
            }

            &:not(:has(*))::after {
                margin-left: 0;
            }
        }
    }
</style>
