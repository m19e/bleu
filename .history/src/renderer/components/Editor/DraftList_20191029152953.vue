<template>
    <div>
        <div class="title">Draft</div>
        <div class="items">
            <div class="item" v-for="(text, title) in draftlist" :key="title">
                <div class="value">{{ title }}:</div>
                <div class="name br">{{ text }}</div>
            </div>
        </div>
    </div>
</template>

<script>
export default {
    data() {
        return {
            draftlist: {
                electron: process.versions.electron,
                name: this.$route.name,
                node: process.versions.node,
                path: this.$route.path,
                platform: require('os').platform(),
                vue: require('vue/package.json').version
            }
        }
    }
}
</script>

<style scoped>
.br::before {
    content: '\A';
    white-space: pre;
}

.title {
    color: #888;
    font-size: 18px;
    font-weight: initial;
    letter-spacing: .25px;
    margin-top: 10px;
}

.items {
    margin-top: 8px;
}

.item {
    display: flex;
    margin-bottom: 6px;
}

.item .name {
    color: #6a6a6a;
    margin-right: 6px;
}

.item .value {
    color: #35495e;
    font-weight: bold;
}
</style>
