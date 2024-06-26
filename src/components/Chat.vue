<template>
    <div class="chat-div w-7/12 grow flex flex-col justify-between">
        <div id="messages-div" class="p-2 w-full h-full" ref="messagesDiv">
            <TriMessage v-for="message in messages" :key="message.id" :username="message.username"
                :content="message.content" :userId="message.userId" :time="message.time" />
        </div>
        <div id="textbox-div" class="p-2 w-full h-auto flex" v-if="activeChannel">
            <TriTextareaScaled v-model="msgInput" @keypress.enter.exact.stop="sendMessage()" ref="textbox" />
            <button id="textbox-send" class="" @click="sendMessage()">></button>
        </div>
    </div>
</template>

<script setup>
import * as trionConfig from '../../trion.config.json';
import { inject, provide, ref, watch } from 'vue';
import TriMessage from './Message.vue';
import TriTextareaScaled from './TextareaScaled.vue';

const messagesDiv = ref(null);
const textbox = ref(null);
const msgInput = ref('');

const messages = inject('messages');
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

let ws;
function connectWS() {
    ws = new WebSocket(`wss://${trionConfig.domain}/`);
    ws.onopen = () => {
        console.log("Connected to WebSocket.");
    }
    ws.onclose = () => {
        setTimeout(() => {
            connectWS();
        }, 300);
    };
    ws.onmessage = (event) => {
        try {
            let json = JSON.parse(event.data);
            printMessage(json);
        } catch (error) { }
    };
    ws.onerror = (event) => {
        console.log(event.message);
    }
}
connectWS();

function sendMessage() {
    let msg = msgInput.value.trim();
    if (msg == "") return;
    //sendMessageLocal(msg); Needs a server OK check
    sendMessageToServer(msg);
    msgInput.value = "";
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
        username: "Deleted user",
        time: formattedTime,
        content: data.content
    }
    if (data.user) {
        message.username = data.user.username;
    }
    messages.value.push(message);
}

watch(messages, (newData, oldData) => {
    if (messagesDiv.value.scrollTop === messagesDiv.value.scrollHeight)
        scrollToBottom();
}, { deep: true, flush: 'post' });

async function getMessages() {
    try {
        const payload = { userId: loginUser.value.id, groupId: activeGroup.value.group.id }
        const res = await fetch(`https://${trionConfig.domain}/channel/${activeChannel.value.id}/messages`, {
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
        scrollToBottom();
    } catch (error) {
        console.log(error);
    }
}

function scrollToBottom() {
    messagesDiv.value.scrollTop = messagesDiv.value.scrollHeight;
}
</script>

<style>
.chat-div {
    background-color: var(--depth-dark1);
}

#messages-div {
    overflow-y: scroll;
    overflow-x: hidden;
}

#textbox-send {
    width: 5%;
}
</style>