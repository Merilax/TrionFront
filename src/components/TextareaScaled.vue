<template>
    <textarea id="textbox-input" class="w-full h-8" name="textbox-input" rows="1" placeholder="Send a message"
        @input.stop="(event) => onInput(event)" v-model="msgInput" @focusin="isFocused = true"
        @focusout="isFocused = false"></textarea>
</template>

<script setup>
import * as trionConfig from '../../trion.config.json';
import { ref, watch } from 'vue';
const msgInput = defineModel();

const isFocused = ref(false);

const onInput = (event) => {
    event.target.style.height = 'auto';
    event.target.style.height = (event.target.scrollHeight) + "px";
}

watch(msgInput, () => {
    if (msgInput.value.length > 200)
        msgInput.value = msgInput.value.slice(0, 200);
});
</script>

<style>
#textbox-input {
    resize: none;
    max-height: 8rem;
}
</style>