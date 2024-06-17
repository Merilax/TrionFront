<template>
    <div id="members-div" class="hidden sm:block w-40 lg:w-44 xl:w-52 col-2 w-2/12 h-full flex flex-col p-1">
        <h1 class="text-xl p-2">Members</h1>
        <Member v-for="member in members" :username="member.user.username" :userId="member.userId" />
    </div>
</template>

<script setup>
import * as trionConfig from '../../trion.config.json';
import { inject, ref, watch } from 'vue';
import Member from './Member.vue';

const activeGroup = inject('activeGroup');
const loginUser = inject('loginUser');
const loginToken = inject('loginToken');

const members = ref([]);

watch(activeGroup, async () => {
    const payload = { userId: loginUser.value.id }
    const json = await fetch(`https://${trionConfig.domain}/group/${activeGroup.value.group.id}/members`, {
        method: "POST",
        body: JSON.stringify(payload),
        headers: {
            "Content-Type": "application/json",
            "Authorization": "Bearer " + loginToken.value
        },
        credentials: "include"
    }).then(res => res.json());

    if (json.ok) {
        members.value = json.data;
    }
});
</script>

<style>
#members-div {
    background-color: var(--depth-dark3);
}
</style>