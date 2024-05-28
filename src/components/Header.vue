<script setup></script>

<template>
    <header class="flex justify-between items-center px-10">
        <span id="logoBox" class="flex items-center"><img src="/trion.svg?url" alt="Trion logo"></span>
    </header>
</template>

<style>
header {
    background-color: var(--depth-dark0);
    height: 8dvh;
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