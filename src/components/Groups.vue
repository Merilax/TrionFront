<template>
    <div id="groups-div" class="w-12 sm:w-14 lg:w-16 flex flex-col items-center p-1">
        <button id="createGroupBtn" @click.stop.prevent="showGroupCreator = !showGroupCreator">+</button>

        <Transition>
            <form v-if="showGroupCreator" id="groupCreator" class="flex flex-col p-2" ref="groupCreator">
                <label for="groupName">Group name:</label>
                <input type="text" id="groupName" name="groupName" v-model="groupCreatorName" />
                <button @click.stop.prevent="createGroup()" class="mt-2">Create group</button>
                <br>
                <label for="groupId">Group ID:</label>
                <input type="text" id="groupId" name="groupId" v-model="groupCreatorId" />
                <button @click.stop.prevent="joinGroup()" class="mt-2">Join group</button>
            </form>
        </Transition>

        <Group v-for="userGroup in groups" :groupName="userGroup.group.name"
            :groupDescription="userGroup.group.description" :groupIcon="userGroup.group.icon"
            :groupId="userGroup.group.id" :groupAllowJoining="userGroup.group.allowJoining" />
    </div>
</template>

<script setup>
import * as trionConfig from '../../trion.config.json';
import { inject, onMounted, provide, ref } from 'vue';
import Group from './GroupIcon.vue';
import Cookies from 'js-cookie';

const loginUser = inject('loginUser');
const loginToken = inject('loginToken');
const loggedIn = inject('loggedIn');

const groupCreator = ref(null);
const groups = ref([]);
const showGroupCreator = ref(false);
const groupCreatorName = ref("");
const groupCreatorId = ref("");

provide('groups', groups);

const payload = { userId: loginUser.value.id }
const res = await fetch(`https://${trionConfig.domain}/user/${loginUser.value.id}/groups`, {
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
}

groups.value = json.data;

onMounted(() => {
    document.addEventListener("click", (e) => {
        if (e.target == groupCreator.value || e.target.parentNode == groupCreator.value) return;
        showGroupCreator.value = false;
    });
});

async function createGroup() {
    if (groupCreatorName.value == "") return;

    const payload = { userId: loginUser.value.id, name: groupCreatorName.value }
    const res = await fetch(`https://${trionConfig.domain}/group/`, {
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

    if (json.ok) {
        groups.value.push(json.data);
        showGroupCreator.value = false;
    }
}

async function joinGroup() {
    if (groupCreatorId.value == "") return;

    const payload = { userId: loginUser.value.id }
    const res = await fetch(`https://${trionConfig.domain}/group/${groupCreatorId.value}/join`, {
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

    if (json.ok) {
        groups.value.push(json.data);
        showGroupCreator.value = false;
    }
}
</script>

<style>
#groups-div {
    background-color: var(--depth-dark5);
}

#createGroupBtn {
    width: 90%;
    border-radius: .6rem;
}

#groupCreator {
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