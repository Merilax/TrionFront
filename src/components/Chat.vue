<template>
    <div class="chat-div w-7/12 flex flex-col justify-between">
        <div id="messages-div" class="p-2 w-full h-full" :ref="messagesDiv">
            <TriMessage v-for="message in messages" :key="message.id"
            :content="message.content"
            :userId="message.userId"
            :time="message.time" />
        </div>
        <div id="textbox-div" class="p-2 w-full h-auto flex">
            <TriTextareaScaled v-model="msgInput" />
            <button id="textbox-send" class="" @click="sendMessage()">></button>
        </div>
    </div>
</template>

<script setup>
import { inject, ref } from 'vue';
import TriMessage from './Message.vue';
import TriTextareaScaled from './TextareaScaled.vue';

const messagesDiv = ref();
const msgInput = ref('');
const messages = ref([]);

const getFormattedDateTime = inject('getFormattedDateTime');
const sendMessageToServer = inject('sendMessageToServer');

const printMessage = (data) => {
    let formattedTime = getFormattedDateTime(data.sentAt);

    let message = {
        id: data.id,
        userId: data.userId,
        time: formattedTime,
        content: data.content
    }

    messages.value.push(message);
    messagesDiv.scrollTop = messagesDiv.scrollHeight;
}

const sendMessage = () => {
    console.log(msgInput);
    let msg = msgInput.value.trim();
    if (msg == "") return;

    //sendMessageLocal(msg); Needs a server OK check
    sendMessageToServer(msg);
}

defineExpose({ printMessage });
</script>

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