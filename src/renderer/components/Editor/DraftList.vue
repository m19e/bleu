<template>
    <!-- <div>
        <div class="title">Draft</div>
        <div class="items">
            <div class="item" v-for="(draft, i) in draftlist" :key="i">
                <div @click="selectDraft(i)" class="name">{{ i + 1 }}.{{ draft.title }}</div>
            </div>
        </div>
    </div> -->
    <div @click="selectDraft(index)" class="name">{{ index + 1 }}.{{ draft.title }}</div>
</template>

<script>
export default {
    props: ['draft', 'index'],
    data() {
        return {
            // draftlist: {
            //     electron: process.versions.electron,
            //     name: this.$route.name,
            //     node: process.versions.node,
            //     path: this.$route.path,
            //     platform: require('os').platform(),
            //     vue: require('vue/package.json').version
            // },
            draftlist: [
                {title: "jrchc_worldend", text: "17:38。\nそれがアタシたちに与えられた時間だった。", modified_at: 300},
                {title: "roomshare", text: "窓辺で文庫本を読む灯織さん。", modified_at: 200},
                {title: "hdcy_afterwork", text: "更衣室にはシンプルな造りのシャワールームがある。", modified_at: 100},
            ]
        }
    },
    mounted() {
        this.$emit('select-draft', this.draftlist[0])
    },
    methods: {
        selectDraft(index) {
            this.$emit('select-draft', this.draftlist[index])
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
    cursor: default;
}

.item .value {
    color: #35495e;
    font-weight: bold;
}


</style>
