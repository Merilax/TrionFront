<template>
    <div id="channels-div" class="w-36 sm:w-40 lg:w-44 xl:w-52 flex flex-col">
        <div v-if="activeGroup" id="channelsGroup" class="text-xl p-1 mb-2">{{ activeGroup.group.name }}</div>

        <button v-if="activeGroup" id="createChannelBtn" class="self-center"
            @click.stop.prevent="showChannelCreator = !showChannelCreator">+</button>

        <form v-if="showChannelCreator" id="channelCreator" class="flex flex-col p-2">
            <label for="channelName">Channel name:</label>
            <input type="text" id="channelName" name="channelName" v-model="channelCreatorName" />
            <button @click.stop.prevent="createChannel()" class="mt-2">Create channel</button>
        </form>
        <Channel v-for="chn in channels" :channelName="chn.name" :channelId="chn.id" />
    </div>
</template>

<script setup>
import Channel from './Channel.vue'
import { inject, ref, watch } from 'vue';

const activeGroup = inject('activeGroup');
const activeChannel = inject('activeChannel');
const loginUser = inject('loginUser');
const loginToken = inject('loginToken');

const channels = ref([]);
const showChannelCreator = ref(false);
const channelCreatorName = ref("");

async function createChannel() {
    if (channelCreatorName.value == "") return;

    const payload = { userId: loginUser.value.id, groupId: activeGroup.value.group.id, name: channelCreatorName.value }
    const json = await fetch(`https://localhost:4111/channel/create`, {
        method: "POST",
        body: JSON.stringify(payload),
        headers: {
            "Content-Type": "application/json",
            "Authorization": "Bearer " + loginToken.value
        },
        credentials: "include"
    }).then(res => res.json());

    if (json.ok) channels.value.push(json.data);
}

watch(activeGroup, async () => {
    const payload = { userId: loginUser.value.id }
    const json = await fetch(`https://localhost:4111/group/${activeGroup.value.group.id}/channels`, {
        method: "POST",
        body: JSON.stringify(payload),
        headers: {
            "Content-Type": "application/json",
            "Authorization": "Bearer " + loginToken.value
        },
        credentials: "include"
    }).then(res => res.json());

    if (json.ok) {
        channels.value = json.data;
    }
});
</script>

<style>
#channels-div {
    background-color: var(--depth-dark3);
    color: var(--txtdark);
}

#channelsGroup {
    background-color: var(--depth-dark4);
    box-shadow: 0 4px 0 var(--color-dark2);
}

#createChannelBtn {
    width: 90%;
    border-radius: .6rem;
}

#channelCreator {
    background-color: var(--depth-dark2);
    right: 0;
    position: absolute;
    box-shadow: 0 5px 5px var(--color-dark2);
}
</style>