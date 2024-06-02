<script setup>
defineProps(['content', 'time', 'userId']);
</script>

<template>
    <div class="messageBox">
        <div class="flex justify-between">
            <span>{{ userId }}</span>
            <span class="text-xs">{{ time }}</span>
        </div>
        <p>{{ content }}</p>
    </div>
</template>

<style>
.messageBox {
    background-color: var(--depth-dark2);
    padding: .4rem 1rem;
    margin: .3rem 0 !important;

}
</style>