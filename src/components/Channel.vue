<template>
    <span class="channel-div w-full my-1 p-1 cursor-pointer flex flex-row justify-between relative"
        @contextmenu.prevent.stop="showMenu = !showMenu" @click="setActiveChannel()">
        {{ channelName }}
        <span class="extra" @click.stop.prevent="showMenu = !showMenu">...</span>
        <div v-if="showMenu" class="dropdown absolute w-full space-y-1">
            <div class="w-full" @click.stop.prevent="showEditor = !showEditor; showMenu = false">Settings</div>
            <div class="w-full" @click.stop.prevent="deleteChannel()">Delete</div>
        </div>
        <Teleport to="#teleport-target">
            <Transition>
                <form v-if="showEditor" id="channelEditor" class="flex flex-col p-2 absolute w-80 space-y-1"
                    ref="clickoutClose">
                    <label for="channelName">Name:</label>
                    <input type="text" id="channelName" name="channelName" v-model="editorName" required />
                    <label for="channelDescription">Description:</label>
                    <input type="text" id="channelDescription" name="channelDescription" v-model="editorDescription" />
                    <button @click.stop.prevent="updateChannel()" class="mt-2">Update channel</button>
                </form>
            </Transition>
        </Teleport>
    </span>
</template>

<script setup>
import * as trionConfig from '../../trion.config.json';
import { inject, onMounted, ref } from 'vue';

const props = defineProps({
    channelName: { type: String, required: true },
    channelDescription: String,
    channelId: { type: Number, required: true }
});

const activeGroup = inject('activeGroup');
const activeChannel = inject('activeChannel');
const loginUser = inject('loginUser');
const loginToken = inject('loginToken');
const channels = inject('channels');

const clickoutClose = ref(null);
const showMenu = ref(false);
const showEditor = ref(false);

const editorName = ref("");
const editorDescription = ref("");

editorName.value = props.channelName;
editorDescription.value = props.channelDescription;

onMounted(() => {
    document.addEventListener("click", (e) => {
        if (e.target == clickoutClose.value || e.target.parentNode == clickoutClose.value) return;
        showEditor.value = false;
    });
});

async function setActiveChannel() {
    const payload = { userId: loginUser.value.id, groupId: activeGroup.value.group.id }
    const json = await fetch(`https://${trionConfig.domain}/channel/${props.channelId}/get`, {
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
        showEditor.value = false;
    }
}

async function deleteChannel() {
    const payload = { userId: loginUser.value.id, groupId: activeGroup.value.group.id }
    const json = await fetch(`https://${trionConfig.domain}/channel/${props.channelId}/delete`, {
        method: "POST",
        body: JSON.stringify(payload),
        headers: {
            "Content-Type": "application/json",
            "Authorization": "Bearer " + loginToken.value
        },
        credentials: "include"
    }).then(res => res.json());

    if (json.ok) {
        channels.value.splice(channels.value.findIndex((chn => { return chn.id === props.channelId })), 1);
        if (activeChannel.value === props.channelId)
            activeChannel.value = null;
    }
}

async function updateChannel() {
    if (editorName.value == "") return;

    const payload = { userId: loginUser.value.id, groupId: activeGroup.value.group.id, name: editorName.value }
    if (editorDescription.value !== "") payload.description = editorDescription.value;

    const json = await fetch(`https://${trionConfig.domain}/channel/${props.channelId}/modify`, {
        method: "POST",
        body: JSON.stringify(payload),
        headers: {
            "Content-Type": "application/json",
            "Authorization": "Bearer " + loginToken.value
        },
        credentials: "include"
    }).then(res => res.json());

    if (json.ok) {
        const selectedIndex = channels.value.findIndex(chn => { return chn.id === props.channelId });
        const selectedChannel = channels.value[selectedIndex];

        selectedChannel.name = payload.name;
        if (payload.description) selectedChannel.description = payload.description;
    }
}
</script>

<style scoped>
.channel-div {
    box-sizing: border-box;
    background-color: var(--depth-dark5);
    box-shadow: 0 1px 2px var(--shadow-dark);
}

.extra {
    display: none;
}

.channel-div:hover .extra {
    display: inline-block;
}

.dropdown {
    top: 2.5rem;
    background-color: var(--depth-dark7);
    z-index: 10;
    box-shadow: 0 0 4px var(--shadow-dark);
}

.dropdown>div {
    padding: .4rem;
}

#channelEditor {
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