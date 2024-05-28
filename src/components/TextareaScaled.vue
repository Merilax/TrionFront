<script setup>
import { ref } from 'vue';

const text = ref("");

function onInput(event) {
    event.target.style.height = 'auto';
    event.target.style.height = (event.target.scrollHeight) + "px";
}

defineExpose({ onInput });
</script>

<template>
    <textarea id="textbox-input" class="w-full h-8" name="textbox-input" rows="1" placeholder="Send a message"
        @input="(event) => onInput(event)">{{ text }}</textarea>
</template>

<script>

</script>

<style>
#textbox-input {
    resize: none;
    max-height: 8rem;
}


</style>