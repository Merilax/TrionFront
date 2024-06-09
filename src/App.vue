<template>
    <Suspense v-if="loggedIn">
        <div>
            <TriHeader />
            <main class="flex">
                <TriGroups />
                <TriChannels />
                <TriChat />
                <TriMembers />
            </main>
        </div>
        <template #fallback>
            Loading...
        </template>
    </Suspense>
    <TriLogin v-else />

</template>

<script setup>
import { provide, ref } from 'vue'
import TriHeader from './components/Header.vue';
import TriGroups from './components/Groups.vue';
import TriChannels from './components/Channels.vue';
import TriMembers from './components/Members.vue';
import TriChat from './components/Chat.vue';
import TriLogin from './components/Login.vue';

const loggedIn = ref(false);
const loginUser = ref(null);
const loginToken = ref(null);
const activeGroup = ref(null);
const activeChannel = ref(null);
const isInDirectChannel = ref(false);
provide('loggedIn', loggedIn);
provide('loginUser', loginUser);
provide('loginToken', loginToken);
provide('activeGroup', activeGroup);
provide('activeChannel', activeChannel);
provide('isInDirectChannel', isInDirectChannel);

const getFormattedDateTime = (timestamp) => {
    let date = new Date(timestamp);
    const hours = date.getHours().toString().padStart(2, 0);
    const minutes = date.getMinutes().toString().padStart(2, 0);
    const seconds = date.getSeconds().toString().padStart(2, 0);
    return `${hours}:${minutes}:${seconds}`;
}

provide('getFormattedDateTime', getFormattedDateTime);
</script>

<style>
:root {
    font-family: Arial, Helvetica, sans-serif;

    --bgdark: #222;
    --bglight: #f2f2f2;

    --txtdark: #eee;
    --txtlight: #111;

    --shadow-dark: #0008;
    --shadow-light: #0028;

    --depth-dark0: #252528;
    --depth-dark1: #303033;
    --depth-dark2: #353538;
    --depth-dark3: #404043;
    --depth-dark4: #454548;
    --depth-dark5: #505053;
    --depth-dark6: #555558;
    --depth-dark7: #606063;
    --depth-dark8: #656568;
    --depth-dark9: #707073;
    --depth-dark10: #757578;
    --depth-dark11: #808083;
    --depth-dark12: #858588;
    --depth-dark13: #909093;

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

label {
    color: var(--txtdark);
    font-size: 1rem;
    padding: .2rem;
}

input,
textarea {
    background-color: var(--depth-dark3);
    border: 0;
    color: var(--txtdark);
    font-size: 1rem;
    padding: .2rem;
}

* {
    color: var(--txtdark);
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
    color: var(--txtdark);
}
</style>
