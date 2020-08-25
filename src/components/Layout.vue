<template>
    <div :class="classPrefix && `${classPrefix}-wrapper`" class="layout-wrapper">
        <div :class="classPrefix && `${classPrefix}-content`" class="content">
            <slot/>
        </div>
        <Nav/>
    </div>
</template>

<script lang="ts">
    export default {
        props: ["classPrefix"],
        name: "Layout"
    };
</script>

<style lang="scss" scoped>
    Nav {
        /*background-color: #fff;*/
    }

    .layout-wrapper {
        display: flex;
        flex-direction: column;
        min-height: 100vh;
    }

    .content {
        flex-grow: 1;
        overflow: auto;
        background-color: #fff;
    }
</style>