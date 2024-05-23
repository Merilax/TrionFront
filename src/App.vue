<script setup>
import TriHeader from './components/Header.vue';
import TriGroups from './components/Groups.vue';
import TriChannels from './components/Channels.vue';
import TriMembers from './components/Members.vue';
import TriChat from './components/Chat.vue';
</script>

<template>
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>Trion</title>
        <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/css/bootstrap.min.css"
            integrity="sha384-QWTKZyjpPEjISv5WaRU9OFeRpok6YctnYmDr5pNlyT2bRjXh0JMhjY6hW+ALEwIH" crossorigin="anonymous">
    </head>

    <body class="grid">
        <TriHeader />
        <main class="container-fluid row m-0 p-0">
            <TriChannels />
            <TriGroups />
            <TriChat />
            <TriMembers />
        </main>
        <script src="/static/src/chat.js"></script>
    </body>

    </html>
</template>

<style>
:root {
    font-family: Arial, Helvetica, sans-serif;

    --bgdark: #222;
    --bglight: #f2f2f2;

    --txtdark: #eee;
    --txtlight: #111;

    --depth-dark0: #242426;
    --depth-dark1: #303032;
    --depth-dark2: #484850;
    --depth-dark3: #606062;
    --depth-dark4: #787880;
    --depth-dark5: #909092;

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

p {
    color: var(--txtdark);
}

body {
    background-color: var(--bgdark);
    overflow: hidden;
}

main {
    height: 92dvh;
}

input {
    background-color: var(--depth-dark1);
    border: 0;
    color: var(--txtdark);
    font-size: 1rem;
}

button {
    background-color: var(--color-dark5);
    border: 0;
}
</style>
