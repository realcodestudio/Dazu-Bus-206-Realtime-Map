<template>
    <div id="background">
        <span id="time">{{ time }}更新</span>
    </div>
</template>

<script setup lang="ts">
defineProps<{
    time: string
}>()
</script>

<style scoped>
#background {
    position: absolute;
    top: 130px;
    left: -6px;
    background-color: rgba(0, 0, 0, 0.7);
    height: 62px;
    width: 480px;
    z-index: 1000;
}

#time {
    color: #fff;
    font-weight: 600;
    font-size: 42px;
    margin-left: 18px;
    line-height: 62px;
}
</style>