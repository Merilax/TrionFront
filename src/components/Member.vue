<template>
    <div class="member-div w-full my-1 p-1 ">{{ username }}</div>
</template>

<script setup>
import { inject } from 'vue';

const props = defineProps(['username', 'userId']);

const activeGroup = inject('activeGroup');
const loginUser = inject('loginUser');
const loginToken = inject('loginToken');

</script>

<style>
.member-div {
    box-sizing: border-box;
    background-color: var(--depth-dark5);
    box-shadow: 0 1px 2px var(--shadow-dark);
}
</style>