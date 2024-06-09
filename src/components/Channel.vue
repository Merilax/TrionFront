<template>
    <span class="channel-div w-full m-1 p-1 cursor-pointer" @click="setActiveChannel()">{{
        channelName }}</span>
</template>

<script setup>
import { inject } from 'vue';

const props = defineProps(['channelName', 'channelId']);

const activeGroup = inject('activeGroup');
const activeChannel = inject('activeChannel');
const loginUser = inject('loginUser');
const loginToken = inject('loginToken');

async function setActiveChannel() {
    const payload = { userId: loginUser.value.id, groupId: activeGroup.value.group.id }
    const json = await fetch(`https://localhost:4111/channel/${props.channelId}`, {
        method: "POST",
        body: JSON.stringify(payload),
        headers: {
            "Content-Type": "application/json",
            "Authorization": "Bearer " + loginToken.value
        },
        credentials: "include"
    }).then(res => res.json());

    if (json.ok) {
        activeChannel.value = json.data;
    }
}
</script>

<style>
.channel-div {
    box-sizing: border-box;
    background-color: var(--depth-dark5);
    box-shadow: 0 1px 2px var(--shadow-dark);
}
</style>