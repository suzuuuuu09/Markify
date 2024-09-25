<template>
  <div id="tool-bar">
    <button class="icon-button"  @mousedown="boldText" aria-label="Bold (Ctrl+Shift+B)">
      <!-- BoldIcon -->
      <img src="@/assets/bold.svg" alt="bold"
        :width="width"
        :height="height"
      >
    </button>
    <button class="icon-button" @mousedown="italicText" aria-label="Italic (Ctrl+Shift+I)">
      <!-- ItalicIcon -->
       <img src="@/assets/italic.svg" alt="italic"
        :width="width"
        :height="height"
       >
    </button>
  </div>
  <div id="editor">
    <textarea id="inputTextarea" v-model="input" @input="update" @mouseup="handleTextSelection"></textarea>
    <div v-html="compiledMarkdown" class="markdown-preview"></div>
  </div>
</template>

<script>
import { marked } from 'marked'
import _ from 'lodash'

export default {
  data() {
    return {
      input: '# Hello World\n\nThis is **bold** text and _italic_ text.' // v-modelで管理されているデータ
    }
  },
  computed: {
    compiledMarkdown() {
      return marked(this.input, { sanitize: true })
    }
  },
  methods: {
    update: _.debounce(function (e) {
      this.input = e.target.value
    }, 300),
    wrapTextWithTags(event, tagText, placeholderText = 'text') {
      event.preventDefault();
      event.stopPropagation();

      // テキストエリアのDOM要素を取得
      const textarea = document.getElementById("inputTextarea");

      // カーソルの開始位置と終了位置を取得
      const start = textarea.selectionStart;
      const end = textarea.selectionEnd;

      // 選択されているテキスト
      const selectedText = this.input.substring(start, end);
      
      // 選択されているテキストがある場合は、そのテキストをタグで囲むまたは消す
      if (selectedText.length > 0) {
        const beforeSelectedText = textarea.value.substring(start - tagText.length, start);
        const afterSelectedText = textarea.value.substring(end, end + tagText.length);
        if (beforeSelectedText === tagText && afterSelectedText === tagText) {
          this.input = 
            this.input.substring(0, start - tagText.length) +
            this.input.substring(start, end) +
            this.input.substring(end + tagText.length);

          // テキストの選択をする
          this.$nextTick(() => {
            textarea.selectionStart = start - tagText.length;
            textarea.selectionEnd = end - tagText.length;
          });
        } else {
          this.input = 
            this.input.substring(0, start) + 
            `${tagText}${selectedText}${tagText}` + 
            this.input.substring(end);

          // テキストの選択をする
          this.$nextTick(() => {
            textarea.selectionStart = start + tagText.length;
            textarea.selectionEnd = end + tagText.length;
          });
        }
      } else {
        // 選択されていない場合は、カーソル位置にタグを挿入し、カーソルをその間に移動
        this.input = 
          this.input.substring(0, start) + 
          `${tagText}${placeholderText}${tagText}` + 
          this.input.substring(end);

        // カーソルをタグの間に移動
        this.$nextTick(() => {
          textarea.selectionStart = start + tagText.length;
          textarea.selectionEnd = start + tagText.length + placeholderText.length;
        });
      }
      textarea.focus();
    },
    // ボールド用の関数
    boldText(event) {
      this.wrapTextWithTags(event, '**', 'strong text');
    },
    // イタリック用の関数
    italicText(event) {
      this.wrapTextWithTags(event, '_', 'emphasized text');
    }
  },
  props: {
    width: {
      type: String,
      default: "10"
    },
    height: {
      type: String,
      default: "10"
    }
  }
}

</script>

<style>
#editor {
  display: flex;
  justify-content: space-between;
  width: 80vw;
  height: 100vh;
  padding: 20px;
  box-sizing: border-box;
  margin: auto;
}

/* テキスト入力 */
textarea {
  width: 48%;
  height: 100%;
  font-size: 16px;
  margin-right: 2%;
  padding: 10px;
  box-sizing: border-box;
  border: 1px solid #ccc;
  border-radius: 10px;
  resize: none;
}

.icon-button{
  background-color: transparent;
  border: none;
  cursor: pointer;
  margin: 0 5px;
}

.markdown-preview {
  font-family: Arial, Helvetica, sans-serif;
  width: 48%;
  padding: 20px;
  box-sizing: border-box;
  overflow-y: auto;
  height: 100%;
  border: 1px solid #ccc;
  border-radius: 10px;
}

.markdown-preview h1 {
  font-size: 2em;
}

.markdown-preview h2 {
  font-size: 1.75em;
}

.markdown-preview p {
  margin: 1em 0;
}

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
