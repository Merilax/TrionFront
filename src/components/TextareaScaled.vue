<template>
    <textarea id="textbox-input" class="w-full h-8" name="textbox-input" rows="1" placeholder="Send a message"
        @input.stop="(event) => onInput(event)" v-model="msgInput"></textarea>
</template>

<script setup>
import * as trionConfig from '../../trion.config.json';
const msgInput = defineModel();

const onInput = (event) => {
    event.target.style.height = 'auto';
    event.target.style.height = (event.target.scrollHeight) + "px";
}
</script>

<style>
#textbox-input {
    resize: none;
    max-height: 8rem;
}
</style>