<template>
    <div id="channels-div" class="w-36 sm:w-40 lg:w-44 xl:w-52 flex flex-col p-1 ">
        <div v-if="activeGroup" id="channelsGroup" class=" p-1 mb-2">
            <h2 class="text-xl">{{ activeGroup.group.name }}</h2>
            <p class="text-sm">Invite ID: {{ activeGroup.group.id }}</p>
        </div>

        <button v-if="activeGroup" id="createChannelBtn" class="self-center"
            @click.stop.prevent="showChannelCreator = !showChannelCreator">+</button>

        <Transition>
            <form v-if="showChannelCreator" id="channelCreator" class="flex flex-col p-2">
                <label for="channelName">Channel name:</label>
                <input type="text" id="channelName" name="channelName" v-model="channelCreatorName" />
                <button @click.stop.prevent="createChannel()" class="mt-2">Create channel</button>
            </form>
        </Transition>
        <Channel v-for="chn in channels" :key="chn.id" :channelName="chn.name" :channelDescription="chn.description"
            :channelId="chn.id" class="" />
    </div>
</template>

<script setup>
import * as trionConfig from '../../trion.config.json';
import Channel from './Channel.vue'
import { inject, provide, ref, watch } from 'vue';

const activeGroup = inject('activeGroup');
const activeChannel = inject('activeChannel');
const loginUser = inject('loginUser');
const loginToken = inject('loginToken');

const channels = ref([]);
const showChannelCreator = ref(false);
const channelCreatorName = ref("");

provide('channels', channels);

async function createChannel() {
    if (channelCreatorName.value == "") return;

    const payload = { userId: loginUser.value.id, groupId: activeGroup.value.group.id, name: channelCreatorName.value }
    const res = await fetch(`https://${trionConfig.domain}/channel/`, {
        method: "POST",
        body: JSON.stringify(payload),
        headers: {
            "Content-Type": "application/json",
            "Authorization": "Bearer " + loginToken.value
        },
        credentials: "include"
    });
    const json = await res.json();

    if (json.ok) channels.value.push(json.data);
}

watch(activeGroup, async () => {
    if (!activeGroup.value) return;
    const payload = { userId: loginUser.value.id }
    const res = await fetch(`https://${trionConfig.domain}/group/${activeGroup.value.group.id}/channels`, {
        method: "POST",
        body: JSON.stringify(payload),
        headers: {
            "Content-Type": "application/json",
            "Authorization": "Bearer " + loginToken.value
        },
        credentials: "include"
    });
    const json = await res.json();

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
    position: absolute;
    right: 3rem;
    bottom: 3rem;
    background-color: var(--depth-dark2);
    box-shadow: 0 5px 5px var(--color-dark2);
}

.v-enter-active,
.v-leave-active {
    transition: all 0.5s ease;
}

.v-enter-from,
.v-leave-to {
    opacity: 0;
    transform: translateY(6rem);
}
</style>