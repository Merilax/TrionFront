<script setup>
defineProps(['message']);
</script>

<template>
    <span class="messageBox">
        <p>{{ message }}</p>
    </span>
</template>