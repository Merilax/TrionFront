<template>
    <div class="messageBox px-2 py-1 my-1">
        <div class="flex justify-between">
            <span>{{ username }}</span>
            <span class="text-xs">{{ time }}</span>
        </div>
        <p class="messageContent text-sm mt-1">{{ content }}</p>
    </div>
</template>

<script setup>
import * as trionConfig from '../../trion.config.json';
defineProps(['content', 'time', 'userId', 'username']);
</script>

<style>
.messageBox {
    background-color: var(--depth-dark3);
    max-width: 100%;
    height: auto;
}
.messageContent {
    white-space: pre-wrap;
    overflow-wrap: break-word;
}
</style>