<template>
  <editor-content :editor="editor" />
</template>

<script lang="ts">
import { Editor, EditorContent } from '@tiptap/vue-3';
import StarterKit from '@tiptap/starter-kit';
import { ShallowRef } from 'vue';

/**
 * 注意: 型エラーを回避するために'Editor'ではなく'ShallowRef<Editor>'を使用している
 * 一時的な修正なので、'@tiptap/vue-3'の問題が解決したら見直す必要がある
 * Docs: https://github.com/ueberdosis/tiptap/issues/1344
 */
type EditorItem = {
  editor: ShallowRef<Editor>,
};

export default {
  components: {
    EditorContent,
  },
  data(): EditorItem {
    return {
      editor: null as unknown as ShallowRef<Editor>,
    };
  },
  mounted() {
    this.editor = new Editor({
      content: '',
      extensions: [
        StarterKit,
      ],
    });
  },
  beforeDestroy() {
    this.editor.destroy();
  },
}
</script>

<style lang="scss">
.ProseMirror {
  > * + * {
    margin-top: 0.75em;
  }
  border: 1px solid #e0e0e0 !important;
  box-sizing: border-box;
  border-radius: 3px;
  padding: 16px;
  font-size: 14px;
  code {
    background-color: rbga(#616161, 0.1);
    color: #616161;
  }
}

.content {
  padding: 1rem 0 0;
  h3 {
    margin: 1rem 0 0.5rem;
  }
  pre {
    border-radius: 5px;
    color: #333;
  }
  code {
    display: block;
    white-space: pre-wrap;
    font-size: 0.8rem;
    padding: 0.75rem 1rem;
    background-color: #e0ecef;
    color: #495057;
  }
}
</style>
