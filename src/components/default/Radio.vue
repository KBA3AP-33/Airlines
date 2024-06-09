<template>
    <div>
        <div class="radio-container" v-for="item in items">
            <input :id="item.text" type="radio" :name="group" v-bind:value="item.value" v-model="select" :disabled="item.disabled">
            <label :for="item.text">- {{ item.text }}</label>
        </div>
    </div>
</template>

<script lang="ts">
    import type { PropType } from 'vue';

    export default {
        name: 'Radio',
        data: () => ({
            select: -1,
        }),
        props: {
            group: {
                type: String as PropType<string>,
                required: true
            },
            items: {
                type: Object as PropType<Array<{ text: string, value: string, disabled: boolean }>>,
                required: true
            }
        },
        watch: {
            select(nextValue) {
                this.$emit('changeValue', { key: this.group, values: [nextValue] });
            }
        }
    }
</script>

<style scoped>
    .radio-container {
        display: flex;
        align-items: center;
        gap: 5px;

        * {
            cursor: pointer;
        }

        &:has(input:disabled) {
            pointer-events: none;
            color: var(--gray-color);
        }
    }
</style>
