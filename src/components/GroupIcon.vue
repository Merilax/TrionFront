<template>
    <div id="groupsDiv" class="relative flex flex-col">
        <img v-if="groupIcon" class="group-div" :src="groupIcon" :alt="groupName" @click="setActiveGroup()" />
        <span v-else class="group-div w-full flex justify-center items-center my-2 cursor-pointer"
            @click="setActiveGroup()" @contextmenu.prevent="showMenu = !showMenu">{{ groupNameRef }}</span>
        <div v-if="showMenu" class="dropdown absolute w-40 space-y-1  ">
            <div class="w-full cursor-pointer" @click.stop="showEditor = !showEditor"> Setings </div>
            <div class="w-full cursor-pointer" @click.stop="leaveGroup()"> Leave group </div>
            <div class="w-full cursor-pointer" @click.stop="deleteGroup()"> Delete group </div>
        </div>
        <Teleport to="#teleport-target">
            <Transition>
                <form v-if="showEditor" id="groupEditor"
                    class="flex flex-col justify-left  p-2 absolute w-80 space-y-1">
                    <label for="groupName">Name:</label>
                    <input type="text" id="groupName" name="groupName" v-model="editorName" required />
                    <label for="groupDescription">Description:</label>
                    <input type="text" id="groupDescription" name="groupDescription" v-model="editorDescription" />
                    <label for="groupAllowJoining">Allow joining:</label>
                    <input type="checkbox" id="groupAllowJoining" name="groupAllowJoining"
                        v-model="editorAllowJoining" />
                    <button @click.stop.prevent="updateGroup()" class="mt-2">Update group</button>
                </form>
            </Transition>
        </Teleport>
    </div>
</template>

<script setup>
import { inject, reactive, ref } from 'vue';

const props = defineProps({
    groupName: {
        type: String,
        required: true
    },
    groupDescription: String,
    groupIcon: String, // Todo
    groupId: {
        type: Number,
        required: true
    },
    groupAllowJoining: {
        type: Boolean,
        required: true
    }
});

const activeGroup = inject('activeGroup');
const loginUser = inject('loginUser');
const loginToken = inject('loginToken');
const groups = inject('groups');

const groupNameRef = ref(props.groupName);
const showMenu = ref(false);
const showEditor = ref(false);

const editorName = ref("");
const editorDescription = ref("");
const editorAllowJoining = ref(false);

editorName.value = props.groupName;
editorDescription.value = props.groupDescription;
editorAllowJoining.value = props.groupAllowJoining;

let groupNamePreview = reactive(groupNameRef.value.toUpperCase().split(' '));
for (let i = 0; i < groupNamePreview.length; i++) {
    groupNamePreview[i] = groupNamePreview[i].slice(0, 1);
};
groupNameRef.value = groupNamePreview.join('').slice(0, 3);

async function setActiveGroup() {
    const payload = { userId: loginUser.value.id }
    const json = await fetch(`https://localhost:4111/group/${props.groupId}/get`, {
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

async function deleteGroup() {
    const payload = { userId: loginUser.value.id }
    const json = await fetch(`https://localhost:4111/group/${props.groupId}/delete`, {
        method: "POST",
        body: JSON.stringify(payload),
        headers: {
            "Content-Type": "application/json",
            "Authorization": "Bearer " + loginToken.value
        },
        credentials: "include"
    }).then(res => res.json());

    if (json.ok) {
        groups.value.splice(groups.value.findIndex((grp => { return grp.id === props.groupId })), 1);
        if (activeGroup.value === props.groupId)
            activeGroup.value = null;
    }
}

async function leaveGroup() {
    const payload = { userId: loginUser.value.id }
    const json = await fetch(`https://localhost:4111/group/${props.groupId}/leave`, {
        method: "POST",
        body: JSON.stringify(payload),
        headers: {
            "Content-Type": "application/json",
            "Authorization": "Bearer " + loginToken.value
        },
        credentials: "include"
    }).then(res => res.json());

    if (json.ok) {
        groups.value.splice(groups.value.findIndex((grp => { return grp.id === props.groupId })), 1);
        if (activeGroup.value === props.groupId)
            activeGroup.value = null;
    }
}

async function updateGroup() {
    if (editorName.value == "") return;

    const payload = { userId: loginUser.value.id, name: editorName.value, allowJoining: editorAllowJoining.value }
    if (editorDescription.value !== "") payload.description = editorDescription.value;

    const json = await fetch(`https://localhost:4111/group/${props.groupId}/modify`, {
        method: "POST",
        body: JSON.stringify(payload),
        headers: {
            "Content-Type": "application/json",
            "Authorization": "Bearer " + loginToken.value
        },
        credentials: "include"
    }).then(res => res.json());

    if (json.ok) {
        const selectedIndex = groups.value.findIndex(grp => { return grp.id === props.channelId });
        const selectedGroup = groups.value[selectedIndex];

        selectedGroup.name = payload.name;
        selectedGroup.allowJoining = payload.allowJoining;
        if (payload.description) selectedGroup.description = payload.description;
    }
}
</script>

<style scoped>
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

.dropdown {
    top: 3.5rem;
    background-color: var(--depth-dark7);
    z-index: 10;
    box-shadow: 0 0 4px var(--shadow-dark);
}

.dropdown>div {
    padding: .4rem;
}

#groupEditor {
    position: absolute;
    right: 3rem;
    bottom: 3rem;
    background-color: var(--depth-dark2);
    box-shadow: 0 5px 5px var(--color-dark2);
}
</style>