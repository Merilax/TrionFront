<template>
    <div id="groupsDiv" class="flex flex-col">
        <img v-if="groupIcon" class="group-div" :src="groupIcon" :alt="groupName" @click="setActiveGroup()" />
        <span v-else class="group-div w-full flex justify-center items-center my-2 cursor-pointer"
            @click="setActiveGroup()">{{
                groupNameRef }}</span>
    </div>
</template>

<script setup>
import { inject, reactive, ref } from 'vue';

const props = defineProps(['groupName', 'groupIcon', 'groupId']);

const activeGroup = inject('activeGroup');
const loginUser = inject('loginUser');
const loginToken = inject('loginToken');

const groupNameRef = ref(props.groupName);

let groupNamePreview = reactive(groupNameRef.value.toUpperCase().split(' '));
for (let i = 0; i < groupNamePreview.length; i++) {
    groupNamePreview[i] = groupNamePreview[i].slice(0, 1);
};
groupNameRef.value = groupNamePreview.join('').slice(0, 3);

async function setActiveGroup() {
    const payload = { userId: loginUser.value.id }
    const json = await fetch(`https://localhost:4111/group/${props.groupId}`, {
        method: "POST",
        body: JSON.stringify(payload),
        headers: {
            "Content-Type": "application/json",
            "Authorization": "Bearer " + loginToken.value
        },
        credentials: "include"
    }).then(res => res.json());

    if (json.ok) {
        activeGroup.value = json.data;
    }
}
</script>

<style>
#groupsDiv {
    width: 90%;
}

.group-div {
    aspect-ratio: 1 / 1;
    object-fit: contain;
    background-color: var(--depth-dark7);
    box-shadow: 0 0 3px var(--shadow-dark);
    border-radius: 40%;
}

.group-div>span {
    font-size: 2rem;
}
</style>