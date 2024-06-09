<template>
    <div>
        <div class="input-container" v-for="item in items" :key="item.text">
            <label class="input-placeholder" :for="item.text">{{ item.text }}</label>
            <input :id="item.text" type="text" :placeholder="item.placeholder" v-model="item.value" :disabled="item.disabled">
        </div>
    </div>
</template>

<script lang="ts">
    import type { PropType } from 'vue';

    export default {
        name: 'InputText',
        props: {
            group: {
                type: String as PropType<string>,
                required: true
            },
            items: {
                type: Object as PropType<Array<{ text: string, value: string, placeholder: string, disabled: boolean }>>,
                required: true
            }
        },
        watch: {
            items: {
                handler(nextValue) {
                    this.$emit('changeValue', { key: this.group, values: nextValue.map((e: { text: string, value: string }) => e.value) });
                },
                deep: true,
            },
        }
    }
</script>

<style scoped>
    .input-container {
        display: flex;
        align-items: baseline;
        gap: 5px;

        .input-placeholder {
            min-width: 20px;
        }
    }
</style>
