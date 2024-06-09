<template>
    <div>
        <div class="checkbox-container" v-for="item in items" :key="item.text">
            <input :id="item.text" type="checkbox" :value="item.value" v-model="selected" :disabled="item.disabled">
            <label :for="item.text">- {{ item.text }}</label>
        </div>
    </div>
</template>

<script lang="ts">
    import type { PropType } from 'vue';

    export default {
        name: 'Checkbox',
        data: () => ({
            selected: [] as Array<number>,
        }),
        props: {
            group: {
                type: String as PropType<string>,
                required: true
            },
            items: {
                type: Object as PropType<Array<{ text: string, value: boolean, disabled: boolean }>>,
                required: true
            }
        },
        watch: {
            selected(nextValue) {
                this.$emit('changeValue', { key: this.group, values: nextValue });
            }
        }
    }
</script>

<style scoped>
    .checkbox-container {
        display: flex;
        align-items: baseline;
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
