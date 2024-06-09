<template>
    <div id="groups-div" class="w-12 sm:w-14 lg:w-16 flex flex-col items-center p-1">
        <button id="createGroupBtn" @click.stop.prevent="showGroupCreator = !showGroupCreator">+</button>
        
        <form v-if="showGroupCreator" id="groupCreator" class="flex flex-col p-2">
            <label for="groupName">Group name:</label>
            <input type="text" id="groupName" name="groupName" v-model="groupCreatorName" />
            <button @click.stop.prevent="createGroup()" class="mt-2">Create group</button>
        </form>

        <Group v-for="userGroup in groups" :groupName="userGroup.group.name" :groupIcon="userGroup.group.icon"
            :groupId="userGroup.group.id" />
    </div>
</template>

<script setup>
import { inject, ref } from 'vue';
import Group from './GroupIcon.vue';

const loginUser = inject('loginUser');
const loginToken = inject('loginToken');

const groups = ref([]);
const showGroupCreator = ref(false);
const groupCreatorName = ref("");

const payload = { userId: loginUser.value.id }
const json = await fetch(`https://localhost:4111/user/${loginUser.value.id}/groups`, {
    method: "POST",
    body: JSON.stringify(payload),
    headers: {
        "Content-Type": "application/json",
        "Authorization": "Bearer " + loginToken.value
    },
    credentials: "include"
}).then(res => res.json());

groups.value = json.data;

async function createGroup() {
    if (groupCreatorName.value == "") return;

    const payload = { userId: loginUser.value.id, name: groupCreatorName.value }
    const json = await fetch(`https://localhost:4111/group/create`, {
        method: "POST",
        body: JSON.stringify(payload),
        headers: {
            "Content-Type": "application/json",
            "Authorization": "Bearer " + loginToken.value
        },
        credentials: "include"
    }).then(res => res.json());

    if (json.ok) groups.value.push(json.data);
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
    background-color: var(--depth-dark2);
    right: 0;
    position: absolute;
    box-shadow: 0 5px 5px var(--color-dark2);
}
</style>