<template>
    <div id="background" :style="{border: `solid 6px #367AF6`}">
        <img src="../assets/image/title.png" class="icon" />
        <span class="title">{{ title }}</span>
    </div>
</template>

<script setup lang="ts">
defineProps<{
    title: string
}>()
</script>

<style scoped>
#background {
    position: absolute;
    top: 50px;
    left: -6px;
    height: 65px;
    background-color: rgba(248, 248, 255);
    color: black;
    padding: 2px 12px;
    font-size: 58px;
    border-radius: 0px;
    z-index: 700;
    border: solid 6px #367AF6;
    font-weight: 600;
    min-width: 495px;
    z-index: 1000;
}

.icon {
    position: relative;
    width: 72px;
    height: 72px;
    margin-right: 12px;
    top: -3px;
}

.title {
    position: relative;
    top: -17px;
}
</style>