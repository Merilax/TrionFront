<script setup></script>

<template>
    <div class="chat-div col-7">
        <div id="messages-div"></div>
        <div id="textbox-div">
            <textarea id="textbox-input" name="textbox-input"></textarea>
            <button id="textbox-send">></button>
        </div>
    </div>
</template>

<style>
.chat-div {
    background-color: var(--depth-dark1);
}

.msgBox {
    margin: 3px 0;
}

.msgBox p {
    width: 90%;
}

#messages-div {
    height: 92%;
    padding: 10px;
    overflow-y: scroll;
}

#textbox-div {
    height: 8%;
    padding: 10px;
    display: flex;
    flex-flow: row nowrap;
    justify-content: space-between;
}

#textbox-input {
    width: 90%;
    resize: none;
}

#textbox-send {
    width: 5%;
}
</style>