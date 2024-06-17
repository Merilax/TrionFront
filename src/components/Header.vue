<template>
    <header class="flex justify-between items-center px-10">
        <span id="logoBox" class="flex items-center"><img src="/trion.svg?url" alt="Trion logo"></span>
        <HeaderLogin />
    </header>
</template>

<script setup>
import * as trionConfig from '../../trion.config.json';
import HeaderLogin from './HeaderLogin.vue';
</script>

<style>
header {
    background-color: var(--depth-dark0);
    height: 8dvh;
    z-index: 100;
}

header>span {
    height: 100%;
    width: 10dvw !important;
}

header img {
    object-fit: contain;
    height: 100%;
    width: 100%;
}

#logoBox img {
    height: 80%;
}
</style>