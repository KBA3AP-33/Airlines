<template>
    <button class="button">
        <slot></slot>
    </button>
</template>

<script lang="ts">
    export default {
        name: 'Button',
    }
</script>

<style scoped>
    .button {
        padding: 4px 50px;
        border: 1px solid var(--black-color);
        transition: background-color .3s linear;
        cursor: pointer;

        @media(hover: hover) {
            &:hover {
                background-color: var(--ligth-gray-color);
            }
        }
    }
</style>
