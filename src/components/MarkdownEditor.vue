<template>
  <div id="container">
    <v-toolbar density="compact" id="toolbar">
      <!-- OpenTextFile -->
      <v-btn icon variant="text" class="icon-button" @mousedown="openTextFile" aria-label="Open Text File (Ctrl+Shift+O)">
        <Icon icon="material-symbols:file-open-outline" :width="width" :height="height" />
        <v-tooltip activator="parent" location="bottom">Open Text File (Ctrl+Shift+O)</v-tooltip>
      </v-btn>
      <input
        type="file"
        ref="fileInput"
        style="display: none;"
        @change="handleFileChange"
        accept=".md, .txt"
      />

      <!-- Bold -->
      <v-btn icon variant="text" class="icon-button"  @mousedown="boldText" aria-label="Bold (Ctrl+Shift+B)">
        <Icon icon="octicon:bold-16" :width="width" :height="height" />
        <v-tooltip activator="parent" location="bottom">Bold (Ctrl+Shift+B)</v-tooltip>
      </v-btn>

      <!-- Italic -->
      <v-btn icon variant="text" class="icon-button" @mousedown="italicText" aria-label="Italic (Ctrl+Shift+I)">
        <Icon icon="bx:italic" :width="width" :height="height" />
        <v-tooltip activator="parent" location="bottom">Italic (Ctrl+Shift+I)</v-tooltip>
      </v-btn>

      <!-- Strikethrough -->
      <v-btn icon variant="text" class="icon-button" @mousedown="strikethroughText" aria-label="Strikethrough (Ctrl+Shift+S)">
        <Icon icon="mingcute:strikethrough-fill" :width="width" :height="height" />
        <v-tooltip activator="parent" location="bottom">Strikethrough (Ctrl+Shift+S)</v-tooltip>
      </v-btn>

      <!-- Heading -->
      <v-btn icon variant="text" class="icon-button" @mousedown="headingText" aria-label="Heading (Ctrl+Shift+H)">
        <Icon icon="gridicons:heading" :width="width" :height="height" />
        <v-tooltip activator="parent" location="bottom">Heading (Ctrl+Shift+H)</v-tooltip>
      </v-btn>
    </v-toolbar>
    <div id="editor">
      <textarea id="input-textarea" v-model="input" @input="update"></textarea>
      <div v-html="compiledMarkdown" class="markdown-preview"></div>
    </div>
  </div>
</template>

<script>
import { marked } from 'marked'
import _ from 'lodash'
import { Icon } from '@iconify/vue'
import { VTooltip } from 'vuetify/components/VTooltip'

export default {
  data() {
    return {
      input: '# Hello World\n\nThis is **bold** text and _italic_ text.',
    }
  },
  computed: {
    compiledMarkdown() {
      return marked(this.input, { sanitize: true })
    }
  },
  components: {
    Icon,
    VTooltip,
  },
  methods: {
    update: _.debounce(function (e) {
      this.input = e.target.value
    }, 300),
    wrapTextWithTags(event, tagText, placeholderText = 'text') {
      event.preventDefault()
      event.stopPropagation()

      // テキストエリアのDOM要素を取得
      const textarea = document.getElementById("input-textarea")

      // カーソルの開始位置と終了位置を取得
      const start = textarea.selectionStart
      const end = textarea.selectionEnd

      // 選択されているテキスト
      const selectedText = this.input.substring(start, end)

      // 選択されているテキストがある場合は、そのテキストをタグで囲むまたは消す
      if (selectedText.length > 0) {
        const beforeSelectedText = textarea.value.substring(start - tagText.length, start)
        const afterSelectedText = textarea.value.substring(end, end + tagText.length)
        if (beforeSelectedText === tagText && afterSelectedText === tagText) {
          this.input =
            this.input.substring(0, start - tagText.length) +
            this.input.substring(start, end) +
            this.input.substring(end + tagText.length)

          // テキストの選択をする
          this.$nextTick(() => {
            textarea.selectionStart = start - tagText.length
            textarea.selectionEnd = end - tagText.length
          })
        } else {
          this.input =
            this.input.substring(0, start) +
            `${tagText}${selectedText}${tagText}` +
            this.input.substring(end)

          // テキストの選択をする
          this.$nextTick(() => {
            textarea.selectionStart = start + tagText.length
            textarea.selectionEnd = end + tagText.length
          })
        }
      } else {
        // 選択されていない場合は、カーソル位置にタグを挿入し、カーソルをその間に移動
        this.input =
          this.input.substring(0, start) +
          `${tagText}${placeholderText}${tagText}` +
          this.input.substring(end)

        // カーソルをタグの間に移動
        this.$nextTick(() => {
          textarea.selectionStart = start + tagText.length
          textarea.selectionEnd = start + tagText.length + placeholderText.length
        })
      }
      textarea.focus()
    },
    // ボールド用の関数
    boldText(event) {
      this.wrapTextWithTags(event, '**', 'strong text')
    },
    // イタリック用の関数
    italicText(event) {
      this.wrapTextWithTags(event, '_', 'emphasized text')
    },
    // 取り消し線
    strikethroughText(event) {
      this.wrapTextWithTags(event, '~~', 'squiggle text')
    },
    openTextFile(){
      this.$refs.fileInput.click()
    },
    handleFileChange(event) {
      const file = event.target.files[0]
      if (file) {
        const reader = new FileReader()
        reader.onload = (e) => {
          this.input = e.target.result
        }
        reader.readAsText(file)
      }
    },
  },
  props: {
    width: {
      type: String,
      default: "1.5em"
    },
    height: {
      type: String,
      default: "1.5em"
    }
  }
}

</script>

<style>

#container {
  display:flex;
  flex-direction: column;
  width: 100vw;
  height: 100vh;
  box-sizing: border-box;
}

#toolbar {
  display: flex;
  background-color: #42474e;
  width: 100%;
  padding: 5px;
  box-sizing: border-box;
  flex-wrap: wrap;
  overflow: hidden;
}

#editor {
  display: flex;
  justify-content: space-between;
  width: 100%;
  height: 100%;
  box-sizing: border-box;
  margin: auto;
  flex-wrap: wrap;
  overflow: hidden;
  color: #333;
}

/* テキスト入力 */
textarea {
  width: 50%;
  height: 100%;
  font-size: 16px;
  padding: 10px;
  box-sizing: border-box;
  resize: none;
  border: none;
  outline: none;
  background-color: #f0f0f3;
  color: #333;
}

.icon-button{
  background-color: transparent;
  border: none;
  cursor: pointer;
  margin: 0 5px;
}

.markdown-preview {
  font-family: Arial, Helvetica, sans-serif;
  width: 50%;
  padding: 20px;
  box-sizing: border-box;
  overflow-y: auto;
  height: 100%;
  background-color: #dfe3eb;
}

.markdown-preview h1 {
  font-size: 2em;
  text-decoration: underline 2px;
}

.markdown-preview h2 {
  font-size: 1.75em;
  text-decoration: underline 1.5px;
}

/* .markdown-preview p {
  margin: 1em 0;
} */

.markdown-preview ul {
  padding-left: 20px;
  list-style-type: disc;
}

.markdown-preview ul li {
  margin-bottom: 0.5em;
}

.markdown-preview strong {
  font-weight: bold;
}

.markdown-preview em {
  font-style: italic;
}

.markdown-preview pre {
  padding: 10px;
  border-radius: 5px;
  overflow-x: auto;
}

.markdown-preview code {
  padding: 2px 4px;
  border-radius: 3px;
  font-family: 'Courier New', Courier, monospace;
  background-color: #666;
}

.markdown-preview table {
  border-collapse: collapse;
}

/* 表の1番上のセル */
.markdown-preview table th {
  border: solid 1px;
  padding: 0.5px 12px;
  font-weight: bold;
  background-color: #333;
}

/* 表のセル */
.markdown-preview table td {
  border: solid 1px;
  padding: 0.5px 12px;
}

.markdown-preview tr:nth-child(odd) {
  background-color: #444;
}

.markdown-preview tr:nth-child(even) {
  background-color: #333;
}

</style>
