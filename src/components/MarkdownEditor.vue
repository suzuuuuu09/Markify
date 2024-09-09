<template>
  <div id="editor">
    <textarea v-model="input" @input="update"></textarea>
    <div v-html="compiledMarkdown" class="markdown-preview"></div>
  </div>
</template>

<script>
import { marked } from 'marked'
import _ from 'lodash'

export default {
  data() {
    return {
      input: '# Hello World\n\nThis is **bold** text and _italic_ text.'
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
    }, 300)
  }
}
</script>

<style>
#editor {
  display: flex;
  justify-content: space-between;
  width: 100%;
  height: 100vh;
  padding: 20px;
  box-sizing: border-box;
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
