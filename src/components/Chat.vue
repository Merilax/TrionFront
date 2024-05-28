<script setup>
import { inject, ref } from 'vue';
import TriMessage from './Message.vue';
import TriTextareaScaled from './TextareaScaled.vue';

const messages = ref([]);
const messagesDiv = document.getElementById("messages-div");

const getFormattedDateTime = inject('getFormattedDateTime');
const printMessage = (data) => {
    let formattedTime = getFormattedDateTime(data.sentAt);
    let text = data.id + " : " + data.userId + " : " + data.content + " : " + formattedTime;

    let message = {
        id: data.id,
        content: text
    }

    messages.value.push(message);

    messagesDiv.scrollTop = messagesDiv.scrollHeight;
}

// (timestamp) => {
//     let date = new Date(timestamp);
//     const hours = date.getHours().toString().padStart(2, 0);
//     const minutes = date.getMinutes().toString().padStart(2, 0);
//     const seconds = date.getSeconds().toString().padStart(2, 0);
//     return `${hours}:${minutes}:${seconds}`;
// }

defineExpose({ printMessage });
</script>

<template>
    <div class="chat-div w-7/12 flex flex-col justify-between">
        <div id="messages-div" class="p-2 w-full h-full">
            <div v-for="message in messages" :key="message.id">
                <TriMessage :message="message.content" />
            </div>
        </div>
        <div id="textbox-div" class="p-2 w-full h-auto flex">
            <TriTextareaScaled />
            <button id="textbox-send" class="" @click="sendMessage()">></button>
        </div>
    </div>
</template>

<script>

</script>

<style>
.chat-div {
    background-color: var(--depth-dark1);
}

.messageBox {
    margin: 3px 0;
}

.messageBox p {
    width: 90%;
}

#messages-div {
    overflow-y: scroll;
}

#textbox-send {
    width: 5%;
}
</style>