<template>
    <div id="loginDiv" class="flex flex-col flex-nowrap justify-center items-center">
        <form id="loginForm" class="flex justify-center flex-col p-4 w-11/12 sm:w-3/5 md:w-2/5 ">
            <label for="username">Username: </label>
            <input type="text" id="username" name="username" required v-model="username" />
            <label for="password">Password: </label>
            <input type="password" id="password" name="password" required v-model="password" /><br />
            <div id="loginButtons" class="flex p-2">
                <button @click.stop.prevent="processLoginForm('login')" id="loginBtn">Login</button>
                <button @click.stop.prevent="processLoginForm('register')" id="registerBtn">Register</button>
            </div>
        </form>

    </div>

</template>

<script setup>
import Cookies from 'js-cookie';
import { inject, ref } from 'vue';

const loginUser = inject('loginUser');
const loginToken = inject('loginToken');
const loggedIn = inject('loggedIn');

const username = ref("");
const password = ref("");

try {
    const authData = JSON.parse(Cookies.get('authData'));
    if (authData) {
        loginUser.value = authData.user;
        loginToken.value = authData.token;
        loggedIn.value = true;
    };
} catch (error) {
    console.log("Bad authentication cookie, login required.");
}

async function processLoginForm(path) {
    if (username.value == "" || password.value == "") return;

    try {
        const payload = { username: username.value, password: password.value };
        const json = await fetch("https://localhost:4111/auth/" + path, {
            method: "POST",
            body: JSON.stringify(payload),
            headers: { "Content-Type": "application/json", },
            credentials: "include"
        }).then(res => res.json());

        if (json.ok) {
            loggedIn.value = true;
            loginUser.value = json.data.user;
            loginToken.value = json.data.token;

            Cookies.set('authData', JSON.stringify(json.data), {
                expires: 1,
                sameSite: true,
                secure: true
            });
        } else
            console.log(json.reason);
    } catch (error) {
        console.log(error);
    }
}
</script>

<style>
#loginDiv {
    height: 100dvh;
}

#loginButtons button {
    margin: 0 .3rem;
    padding: .5rem;
    border-radius: .3rem;
    width: 50%;
}

#loginForm {
    background-color: var(--depth-dark1);
    box-shadow: 0 5px 5px var(--color-dark2);
}

#loginForm label,
#loginForm input {
    margin: .2rem 0;
    color: var(--txtdark);
}
</style>
