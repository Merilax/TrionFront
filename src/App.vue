<template>
    <TriHeader />
    <main class="flex columns-4">
        <TriGroups />
        <TriChannels />
        <TriChat ref="ChatRef" :getFormattedDateTime=getFormattedDateTime />
        <TriMembers />
    </main>
</template>

<script setup>
import { provide, ref } from 'vue'
import TriHeader from './components/Header.vue';
import TriGroups from './components/Groups.vue';
import TriChannels from './components/Channels.vue';
import TriMembers from './components/Members.vue';
import TriChat from './components/Chat.vue';

const ChatRef = ref(TriChat);
const printMessage = (msg) => ChatRef.value.printMessage(msg);

const ws = new WebSocket('wss://' + 'localhost:4111' + '/');

ws.addEventListener("open", () => {
    console.log("Connected to WebSocket.");
    retrieveMessageHistory();
});

ws.onmessage = (event) => {
    console.log(event.data);

    try {
        let json = JSON.parse(event.data);
        if (!json.ok) {
            console.log("Server return error: " + json.reason);
            return;
        }
        switch (json.method) {
            case "sendMessage":
                printMessage(json.data);
                break;

            case "getMessageHistory":
                json.data.forEach(msg => {
                    printMessage(msg);
                });
                break;

            default:
                console.log("Unrecognised server method: " + json.method);
                break;
        }
    } catch (error) {
        console.log(error);
    }
};

function sendMessage(msgInput) {
    let msg = msgInput.value.trim();
    if (msg == "") return;

    //sendMessageLocal(msg); Needs a server OK check
    sendMessageToServer(msg);
}

function sendMessageToServer(msg) {
    let json = { userId: "0", method: "sendMessage", messageContent: msg, timestamp: Date.now() };

    ws.send(JSON.stringify(json));
}

function sendMessageLocal(msg) {
    printMessage(msg);
    //printMessage(msg);
}

function retrieveMessageHistory() {
    let json = { method: "getMessageHistory" }

    ws.send(JSON.stringify(json));
}

const getFormattedDateTime = (timestamp) => {
    let date = new Date(timestamp);
    const hours = date.getHours().toString().padStart(2, 0);
    const minutes = date.getMinutes().toString().padStart(2, 0);
    const seconds = date.getSeconds().toString().padStart(2, 0);
    return `${hours}:${minutes}:${seconds}`;
}

provide('getFormattedDateTime', getFormattedDateTime);
provide('sendMessageToServer', sendMessageToServer);
</script>

<style>
:root {
    font-family: Arial, Helvetica, sans-serif;

    --bgdark: #222;
    --bglight: #f2f2f2;

    --txtdark: #eee;
    --txtlight: #111;

    --depth-dark0: #242426;
    --depth-dark1: #303032;
    --depth-dark2: #484850;
    --depth-dark3: #606062;
    --depth-dark4: #787880;
    --depth-dark5: #909092;

    --color-dark1: #246;
    --color-dark2: #358;
    --color-dark3: #46a;
    --color-dark4: #57c;
    --color-dark5: #68e;
}

@supports selector(::-webkit-scrollbar) {
    *::-webkit-scrollbar {
        background: transparent;
    }

    *::-webkit-scrollbar-thumb {
        background: #246;
    }
}

p,
span {
    color: var(--txtdark);
}


body {
    background-color: var(--bgdark);
    overflow: hidden;
}

main {
    height: 92dvh;
}

input,
textarea {
    background-color: var(--depth-dark2);
    border: 0;
    color: var(--txtdark);
    font-size: 1rem;
    padding: .2rem;
}

*:focus {
    outline: none;
}

input:focus,
textarea:focus {
    box-shadow: var(--color-dark5) 0 0 5px;
    transition: all .3s;
}

button {
    background-color: var(--color-dark5);
    border: 0;
}
</style>
