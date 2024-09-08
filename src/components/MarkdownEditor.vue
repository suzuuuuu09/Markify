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

<style scoped>
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
  border-radius: 5px;
  resize: none;
}

.markdown-preview {
  width: 48%;
  padding: 20px;
  box-sizing: border-box;
  overflow-y: auto;
  height: 100%;
}

.markdown-preview h1 {
  font-size: 2em;
  color: #333;
  border-bottom: 2px solid #ccc;
  padding-bottom: 0.5em;
}

.markdown-preview h2 {
  font-size: 1.75em;
  color: #666;
  border-bottom: 1px solid #ccc;
  padding-bottom: 0.3em;
}

.markdown-preview p {
  margin: 1em 0;
  color: #444;
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
  color: #000;
}

.markdown-preview em {
  font-style: italic;
  color: #888;
}

.markdown-preview pre {
  background-color: #f4f4f4;
  padding: 10px;
  border-radius: 5px;
  overflow-x: auto;
}

.markdown-preview code {
  background-color: #f4f4f4;
  padding: 2px 4px;
  border-radius: 3px;
  font-family: 'Courier New', Courier, monospace;
  color: #d6336c;
  background-color: #888;
}

code {
  font-family: 'Courier New', Courier, monospace;
  color: #d6336c;
}
</style>
