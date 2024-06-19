<template>
    <div id="headerLogin" class="flex items-center h-full">
        <span class="m-2">Welcome, {{ username }}</span>
        <button class="p-2 ml-3" @click.stop.prevent="logout()">Logout</button>
    </div>
</template>

<script setup>
import * as trionConfig from '../../trion.config.json';
import Cookies from 'js-cookie';
import { inject } from 'vue';

const loginUser = inject('loginUser');
const loginToken = inject('loginToken');
const loggedIn = inject('loggedIn');
const activeGroup = inject('activeGroup');
const activeChannel = inject('activeChannel');
const messages = inject('messages');

let username = loginUser.value.username;

function logout() {
    Cookies.remove('authData');
    loginUser.value = null;
    loginToken.value = null;
    loggedIn.value = false;
    activeGroup.value = null;
    activeChannel.value = null
    messages.value = [];
}
</script>

<style></style>