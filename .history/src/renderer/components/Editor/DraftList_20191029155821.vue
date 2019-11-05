<template>
    <div>
        <div class="title">Draft</div>
        <div class="items">
            <div class="item" v-for="(text, title) in draftlist" :key="title">
                <div class="name">{{ title }}:</div>
                <div class="value">{{ text }}</div>
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

html {
  font-family: 'Yu Mincho', YuMincho, 'Hiragino Mincho ProN', 'Hiragino Mincho Pro', 'HGP明朝B', serif;
  -webkit-font-feature-settings: 'pkna';
          font-feature-settings: 'pkna';
  font-size: 17px;
  line-height: 1.9;
  letter-spacing: 0.03em;
  -webkit-writing-mode: vertical-rl;
      -ms-writing-mode: tb-rl;
          writing-mode: vertical-rl;
}
p {
  text-indent: 1em;
  margin-left: 30px;
}
ruby {
  font-family: -apple-system, BlinkMacSystemFont, "Helvetica Neue", "Yu Gothic", YuGothic, Verdana, Meiryo, "M+ 1p", sans-serif;
}
</style>
