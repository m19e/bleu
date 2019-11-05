<template>
    <div id="wrapper">
        <main>
            <div class="left-side" v-on:mouseover="zen = false">
                <!-- <draft-list v-on:select-draft="putDraftData"></draft-list> -->
                <div class="info" :class="{'is-zen': zen}">
                    <div class="title" style="color:white;">
                        <span style="color:white;">Draft</span><span @click="createDraft" class="arrow" style="margin-left:3px;font-weight:bold;">＋</span><span @click="confirm = !confirm" class="arrow" style="font-weight:bold;">−</span><span v-show="confirm" @click="deleteDraft" class="arrow" style="font-size:0.7em;font-weight:bold;">クリックで削除</span>
                    </div>
                    <div class="items">
                        <div class="item" v-for="(draft, i) in draftlist" :key="i">
                            <div @click="putDraftData(i)" class="name" style="color:white;font-style:bold;font-size:1.2em;width:200px;">{{ i + 1 }}.{{ draft.title }}</div>
                            <!-- <draft-list :index="i" :draft="draft"></draft-list> -->
                        </div>
                    </div>
                </div>
                <!-- <button @click="saveDraft">保存</button> -->
            </div>

            <div class="right-side">
                <input v-model="draft.title" placeholder="タイトル"></input>
                <div class="part">
                    <textarea v-on:mouseover="zen = true" v-model="draft.text" id="tate" cols="64" rows="23" placeholder="本文"></textarea>
                </div>
            </div>
        </main>
    </div>
</template>

<script>
import DraftList from './Editor/DraftList'
const Store = require('electron-store')
const store = new Store()

export default {
    name: 'editor',
    components: { DraftList },
    data() {
        return {
            draftlist: [
                {id: 0, title: "283プロダクション", lines: ['イルミネーションスターズ','アンティーカ','アルストロメリア','放課後クライマックスガールズ','ストレイライト'], created_at: 0, modified_at: 100},
                {id: 1, title: "346プロダクション", lines: ['エルドリッチ・ロアテラー','Dimension-3',"カワイイボクと142's"], created_at: 1, modified_at: 200}
                // {id: 1, title: "jrchc_worldend", text: "17:38。\nそれがアタシたちに与えられた時間だった。", created_at: 300},
                // {id: 2, title: "roomshare", text: "窓辺で文庫本を読む灯織さん。", created_at: 200},
                // {id: 3, title: "hdcy_afterwork", text: "更衣室にはシンプルな造りのシャワールームがある。", created_at: 100},
            ],
            draft: {
                title: '',
                text: '',
            },
            editting: false,
            confirm: false,
            zen: false,
            selectIndex: 0
        }
    },
    watch: {
        draft: {
            handler: function (val, oldVal) {
                if (this.editting) {
                    this.saveDraft()
                    this.zen = true
                    this.selectIndex = 0
                    store.set('draftlist', this.draftlist)
                } else {
                    this.editting = true
                }
            },
            deep: true
        }
    },
    created () {
        
    },
    mounted () {
        this.$nextTick(function () {
            // document.onkeydown = e => {
            //     if (e.key == "s" && (e.metaKey || e.ctrlKey)) {
            //         if (savable) this.saveDraft()
            //         return false;
            //     }
            // }
            // store.set('unicorn', '処女厨')
            store.get('draftlist').map(d => console.log(JSON.stringify(d)))
            this.draftlist = store.get('draftlist').slice().sort(this.compare('modified_at', 'desc'))
            // this.$emit('init-textarea')
            this.putDraftData(0)
        })

    //   document.onkeydown = e => {
    //   	if (Object.keys(this.keymap).includes(e.key) && e.ctrlKey) {
    //   		e.preventDefault()
    //   		console.log(e.keyCode, e.key, this.keymap[`${e.key}`])
    //   		document.getElementById("tate").dispatchEvent(new KeyboardEvent("keydown", {
    //   			keyCode: 40,
    //   			// key: this.keymap[`${e.key}`],
    //   			// ctrlKey: false
    //   		}))
    //   	}
    //   }
    },
    updated () {
        // if (this.editting) {
        //     store.set('draftlist', this.draftlist)
        //     console.log(store.get('draftlist'))
        // } else {
        //     this.editting = true
        // }
    },
    beforeDestroy () {

    },
    computed: {
        adjustTitle(title) {
            if (title.length > 15) {
                return '長すぎるタイトル'
            } else {
                return title
            }
        }
    },
    methods: {
        open(link) {
            this.$electron.shell.openExternal(link)
        },
        eventHandler(event) {
            // event.preventDefault();
            // this.$emit(this.keys[`${event.key}`])
            // this.$emit(new KeyboardEvent( "keydown", {key: this.keys[`${event.key}`]}))
            // document.dispatchEvent( new KeyboardEvent( "keydown", { key: this.keys[`${event.key}`], ctrlKey: false }))
            console.log(event ,event.key, this.keys[`${event.key}`]);
            // console.log(event.key, '->', this.keys.event.key);
        },
        showEvent(title) {
            console.log('Selected:', title);
        },
        lines2text(array) {
            return array.join('\n')
        },
        text2lines(text) {
            return text.split('\n')
        },
        putDraftData(index) {
            this.editting = false
            this.draft.title = this.draftlist[index].title
            this.draft.text = this.lines2text(this.draftlist[index].lines)
            this.selectIndex = index
        },
        compare(key, order='asc') {
            return function(a, b) {
                if(!a.hasOwnProperty(key) || !b.hasOwnProperty(key)) {
                  // property doesn't exist on either object
                    return 0;
                }
                let varA = (typeof a[key] === 'string') ?
                    a[key].toUpperCase() : a[key];
                let varB = (typeof b[key] === 'string') ?
                    b[key].toUpperCase() : b[key];
                let comparison = 0;
                if (varA > varB) {
                    comparison = 1;
                } else if (varA < varB) {
                    comparison = -1;
                }
                return (
                  (order == 'desc') ? (comparison * -1) : comparison
                );
            };
        },
        sortDraftlist() {
            this.draftlist = this.draftlist.slice().sort(this.compare('modified_at', 'desc'))
        },
        saveDraft() {
            // 先に並び変えてから色々したいよね(index = 0が毎回最新更新なので)
            this.editting = true
            this.draftlist[this.selectIndex].modified_at = Date.now()
            this.sortDraftlist()
            this.draftlist[0].title = this.draft.title
            this.draftlist[0].lines = this.text2lines(this.draft.text)
            console.log('save draft:', JSON.stringify(this.draftlist[0]));
        },
        createDraft() {
            this.draftlist.unshift({title: "新しいページ", lines: ['']})
            this.draftlist[0].created_at = Date.now()
            this.draftlist[0].modified_at = Date.now()
            this.putDraftData(0)
        },
        deleteDraft() {
            this.editting = true
            this.draftlist[this.selectIndex].modified_at = Date.now()
            this.sortDraftlist()
            this.draftlist.shift()
            this.putDraftData(0)
            this.confirm = false
            store.set('draftlist', this.draftlist)
        },
    }
}
</script>

<style>
/* @import url('https://fonts.googleapis.com/css?family=Source+Sans+Pro'); */
@font-face {
	font-family: 'GenEiKoburiMin';
	src: url(../assets/GenEiKoburiMin6-R.ttf);
}
* {
    box-sizing: border-box;
    margin: 0;
    padding: 0;
}

body {
    /* font-family: 'Source Sans Pro', sans-serif; */
    font-family: GenEiKoburiMin;
}

#wrapper {
    /* background: radial-gradient( ellipse at top left, rgba(255, 255, 255, 1) 40%, rgba(229, 229, 229, .9) 100%); */
    /* background-color: #bce2e8; */
    /* opacity: 0.8; */
    background-color: #6dcae2;
    height: 100vh;
    padding: 40px 80px;
    width: 100vw;
}

#logo {
    height: auto;
    margin-bottom: 20px;
    width: 420px;
}

main {
    display: flex;
    justify-content: space-between;
}

main>div {
    flex-basis: 50%;
}

.arrow {
    cursor: default;
}

.left-side {
    display: flex;
    flex-direction: column;
}

.info {
    transition: opacity 2s, visibility 2s;
    opacity: 1;
    visibility: visible;
}

.info.is-zen {
    opacity: 0;
    visibility: hidden;
}

.welcome {
    color: #555;
    font-size: 23px;
    margin-bottom: 10px;
}

.title {
    color: #2c3e50;
    font-size: 20px;
    font-weight: bold;
    margin-bottom: 6px;
}

.title.alt {
    font-size: 18px;
    margin-bottom: 10px;
}

.doc p {
    color: black;
    margin-bottom: 10px;
}

.doc button {
    font-size: .8em;
    cursor: pointer;
    outline: none;
    padding: 0.75em 2em;
    border-radius: 2em;
    display: inline-block;
    color: #fff;
    background-color: #4fc08d;
    transition: all 0.15s ease;
    box-sizing: border-box;
    border: 1px solid #4fc08d;
}

.doc button.alt {
    color: #42b983;
    background-color: transparent;
}

.right-side {
    width: 100%;
}

input {
    font-family: GenEiKoburiMin;
    width: 100%;
    font-size: 1.5em;
    padding: 10px;
    border: 0;
    border-top-left-radius: 4px;
    border-top-right-radius: 4px;
}

input:focus {
    outline: 0;
}

.part {
    /* writing-mode: vertical-rl; */
    /* margin: 0 10px 0 10px; */
    
}

textarea {
    /* background: transparent; */

    /* writing-mode: initial;
    transform: rotate(90deg);
    transform-origin: top left;
    cursor: vertical-text; */
    font-family: GenEiKoburiMin;
    font-size: 1em;
    padding: 10px;
    border: 0;
    resize: none;
    border-bottom-left-radius: 4px;
    border-bottom-right-radius: 4px;
    line-height: 1.5em;
}

textarea:focus {
    outline: 0;
}

p {
    text-orientation: upright;
}

.title {
    color: #888;
    font-size: 18px;
    font-weight: initial;
    letter-spacing: .25px;
    margin-top: 10px;
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