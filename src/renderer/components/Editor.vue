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
                            <!-- <div @click="putDraftData(i)" class="name" style="color:white;font-style:bold;font-size:1.2em;white-space:nowrap;">{{ shortenTitle(draft.title) }}</div> -->
                            <div @click="putDraftData(i)" class="name">{{ draft.title }}</div>
                        </div>
                    </div>
                </div>
            </div>

            <div class="center-side">
                <input v-model="draft.title" placeholder="タイトル"></input>
                <div class="part">
                    <textarea v-on:mouseover="zen = true" v-model="draft.text" id="tate" cols="70" rows="27" placeholder="本文"></textarea>
                </div>
            </div>
            <div class="right-side">
                <div v-for="line in splitText(text2lines(convertRoundKakko(draft.text)))">
                    <p>{{ line }}</p>
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
            ],
            draft: {
                title: '',
                text: '',
            },
            preview: [['とりあえず'],['へいへい']],
            editting: false,
            confirm: false,
            zen: false,
            selectIndex: 0,
            chars: 42,
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
            // console.log(event ,event.key, this.keys[`${event.key}`]);
            // console.log(event.key, '->', this.keys.event.key);
        },
        shortenTitle(title) {
            // return [...title].map((char, i) => {
            //     if (i < 15) return ''
            //     return char
            // }).join('')
            let limit = 10 // 20
            let count = title.length
            // [...title].forEach(function(char) {
            //     count += this.judgeCharWidth(char)
            // })
            if (count === limit) {
                return title
            } else {
                return title.substring(0, limit) + (title.length > limit ? '…' : '')
            }
        },
        judgeCharWidth(char) {
            if (!!(char.match(/^[^\x01-\x7E]+$/))) return 2
            if (!!(char.match(/^[\xA1-\xDF]+$/) || input.match(/^[a-zA-Z]+$/))) return 1
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
        convertRoundKakko(text) {
            return text.replace(/\(/g, '（').replace(/\)/g, '）')
        },
        putDraftData(index) {
            this.editting = false
            this.draft.title = this.draftlist[index].title
            this.draft.text = this.lines2text(this.draftlist[index].lines)
            // this.preview = this.putPreview(this.draftlist[index].lines.slice())
            this.selectIndex = index
        },
        splitText(lines, result = []) {
            if (lines.length == 0) return result
            let line = lines.shift()
            result.push(line.slice(0, this.chars + 1))
            if (line.length > this.chars) lines.unshift(line.slice(this.chars + 1, line.length))
            return this.splitText(lines, result)
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
            // this.preview = this.putPreview(this.draftlist[0].lines.slice())
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
@import url('https://fonts.googleapis.com/css?family=Source+Sans+Pro');
@font-face {
	font-family: 'GenEiKoburiMin';
	src: url(../assets/GenEiKoburiMin6-R.ttf);
}
* {
    box-sizing: border-box;
    margin: 0;
    padding: 0;
    overflow: none;
}

body {
    /* font-family: 'Source Sans Pro', sans-serif; */
    font-family: GenEiKoburiMin;
    background-color: #bce2e8;
}

#wrapper {
    /* background: radial-gradient( ellipse at top left, rgba(255, 255, 255, 1) 40%, rgba(229, 229, 229, .9) 100%); */
    background-color: #bce2e8;
    /* opacity: 0.8; */
    /* background-color: #6dcae2; */
    height: 100%;
    width: 100%;
    padding: 25px;
}

#logo {
    height: auto;
    margin-bottom: 20px;
    /* width: 420px; */
}

main {
    display: flex;
    justify-content: space-between;
    width: 1200px;
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
    max-width: 220px;
    max-height: 704px;
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

.center-side {
    display: flex;
    flex-direction: column;
}

input {
    font-family: GenEiKoburiMin;
    width: 100%;
    font-size: 1.5em;
    padding: 8px;
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
    /* font-family: GenEiKoburiMin; */
    font-family: 'Source Sans Pro', sans-serif;
    font-size: 1em;
    padding: 8px;
    border: 0;
    resize: none;
    border-bottom-left-radius: 5px;
    border-bottom-right-radius: 5px;
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
    /* color: #6a6a6a; */
    margin-right: 8px;
    cursor: default;
    width: 192px;
    color:white;
    font-weight:bold;
    font-size:1.2em;
    white-space:wrap;
}

.item .name:hover {
    color: #7b7b7b;
}

.item .value {
    color: #35495e;
    font-weight: bold;
}

::-webkit-scrollbar{
  width: 8px;
  height: 8px;
}
::-webkit-scrollbar-track{
  /* background: #fff; */
  background-color: #bce2e8;
  border: none;
  border-radius: 10px;
  box-shadow: inset 0 0 2px #777;
  display: none;
}

::-webkit-scrollbar-track-piece {
    background-color: #bce2e8;
}

::-webkit-scrollbar-thumb {
  background: #ccc;
  border-radius: 10px;
  box-shadow: none;
}

/* .right-side {
    flex: 0 0 470px;
    flex-direction: row;
    height: 704px;
    overflow-y: scroll;
    writing-mode: vertical-rl;
    padding: 8px;
    padding-right: 30px;
} */

.right-side {
    writing-mode: vertical-rl;
    flex: 0 0 540px;
    height: 704px;
    overflow-y: scroll;
    /* border: 2px solid orangered; */
    margin: 0 8px;
    padding: 8px;
    columns: 1 auto;
    column-gap: 8px;
    column-rule: 1px dashed orangered;
    background-color: #eee;
    /* font-family: HiraMinProN-W3, IPA明朝, Meiryo, メイリオ; */
    font-weight: normal;
    font-style: normal;
    font-variant: inherit;
}

.right-side p {
    margin: 0;
    padding: 0 8px;
}
</style>