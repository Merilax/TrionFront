<template>
    <div class="chat-div w-7/12 grow flex flex-col justify-between">
        <div id="messages-div" class="p-2 w-full h-full" :ref="messagesDiv">
            <TriMessage v-for="message in messages" :key="message.id" :username="message.username"
                :content="message.content" :userId="message.userId" :time="message.time" />
        </div>
        <div id="textbox-div" class="p-2 w-full h-auto flex" v-if="activeChannel">
            <TriTextareaScaled v-model="msgInput" />
            <button id="textbox-send" class="" @click="sendMessage()">></button>
        </div>
    </div>
</template>

<script setup>
import { inject, ref, watch } from 'vue';
import TriMessage from './Message.vue';
import TriTextareaScaled from './TextareaScaled.vue';

const messagesDiv = ref();
const msgInput = ref('');
const messages = ref([]);

const loginUser = inject('loginUser');
const loginToken = inject('loginToken');
const loggedIn = inject('loggedIn');

const getFormattedDateTime = inject('getFormattedDateTime');
const activeGroup = inject('activeGroup');
const activeChannel = inject('activeChannel');

watch(activeChannel, () => {
    messages.value = [];
    if (!activeChannel.value) return;
    getMessages();
});

const ws = new WebSocket('wss://' + 'localhost:4111' + '/');

ws.addEventListener("open", () => {
    console.log("Connected to WebSocket.");
});

ws.onmessage = (event) => {
    console.log(event.data);

    try {
        let json = JSON.parse(event.data);
        printMessage(json);

    } catch (error) {
        console.log(error);
    }
};

function sendMessage() {
    let msg = msgInput.value.trim();
    if (msg == "") return;
    //sendMessageLocal(msg); Needs a server OK check
    sendMessageToServer(msg);
}

function sendMessageToServer(msg) {
    let json = { token: loginToken.value, userId: loginUser.value.id, groupId: activeGroup.value.group.id, channelId: activeChannel.value.id, content: msg, sentAt: Date.now() };
    ws.send(JSON.stringify(json));
}

function sendMessageLocal(msg) {
    printMessage(msg);
    //printMessage(msg);
}

function printMessage(data) {
    let formattedTime = getFormattedDateTime(data.sentAt);

    let message = {
        id: data.id,
        userId: data.userId,
        username: data.user.username,
        time: formattedTime,
        content: data.content
    }

    messages.value.push(message);
    messagesDiv.scrollTop = messagesDiv.scrollHeight;
}

async function getMessages() {
    try {
        const payload = { userId: loginUser.value.id, groupId: activeGroup.value.group.id }
        const res = await fetch(`https://localhost:4111/channel/${activeChannel.value.id}/messages`, {
            method: "POST",
            body: JSON.stringify(payload),
            headers: {
                "Content-Type": "application/json",
                "Authorization": "Bearer " + loginToken.value
            },
            credentials: "include"
        });
        const json = await res.json();
        if (res.status == 401) {
            Cookies.remove('authData');
            loginUser.value = null;
            loginToken.value = null;
            loggedIn.value = false;
            return;
        }
        json.data.forEach(msg => printMessage(msg));
    } catch (error) {
        console.log(error);
    }
}
</script>

<style>
.chat-div {
    background-color: var(--depth-dark1);
}

#messages-div {
    overflow-y: scroll;
}

#textbox-send {
    width: 5%;
}
</style>