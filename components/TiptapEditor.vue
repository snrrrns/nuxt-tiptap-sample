<script setup lang="ts">
import { ref, onMounted, onBeforeUnmount } from 'vue';
import { Editor, EditorContent } from '@tiptap/vue-3';
import StarterKit from '@tiptap/starter-kit';
import Collaboration from '@tiptap/extension-collaboration';
import CollaborationCursor from '@tiptap/extension-collaboration-cursor';
import { WebsocketProvider } from 'y-websocket';
import * as Y from 'yjs';

enum UserColor {
  BLUE = '#958DF1',
  RED = '#F98181',
  YELLOW = '#FBBC88',
  ORANGE = '#FAF594',
  GREEN = '#70CFF8',
  LIGHT_BLUE = '#94FADB',
  LIGHT_GREEN = '#B9F18D',
}

const editor = ref<Editor | undefined>();
const provider = ref<WebsocketProvider | undefined>();
const authorName = ref('anonymous');

/**
 * 共同編集者のカーソルの色をランダムで取得
 * 
 * @returns {UserColor} 色コード
 */
function getRandomColor(): UserColor {
  const list = [
    UserColor.BLUE,
    UserColor.RED,
    UserColor.YELLOW,
    UserColor.ORANGE,
    UserColor.GREEN,
    UserColor.LIGHT_BLUE,
    UserColor.LIGHT_GREEN,
  ];
  return list[Math.floor(Math.random() * list.length)];
}

onMounted(() => {
  const ydoc = new Y.Doc;

  provider.value = new WebsocketProvider(
    'ws://localhost:1234',
    'sample-document',
    ydoc);

  editor.value = new Editor({
    content: '',
    extensions: [
      StarterKit,
      Collaboration.configure({
        document: ydoc,
      }),
      CollaborationCursor.configure({
        provider: provider.value,
        user: {
          name: authorName,
          color: getRandomColor(),
        }
      }),
    ],
  });
});

onBeforeUnmount(() => {
  editor.value?.destroy();
  provider.value?.destroy();
});
</script>

<template>
  <input type="text" v-model="authorName">
  <editor-content :editor="editor" />
</template>

<style lang="scss">
/* Basic editor styles */
.ProseMirror {
  > * + * {
    margin-top: 0.75em;
  }
  border: 1px solid #e0e0e0 !important;
  box-sizing: border-box;
  border-radius: 3px;
  padding: 16px 16px 16px 16px;

  ul,
  ol {
    padding: 0 1rem;
  }

  h1,
  h2,
  h3,
  h4,
  h5,
  h6 {
    line-height: 1.1;
  }

  code {
    background-color: rgba(#616161, 0.1);
    color: #616161;
  }

  pre {
    background: #0d0d0d;
    color: #fff;
    font-family: "JetBrainsMono", monospace;
    padding: 0.75rem 1rem;
    border-radius: 0.5rem;

    code {
      color: inherit;
      padding: 0;
      background: none;
      font-size: 0.8rem;
    }
  }

  mark {
    background-color: #faf594;
  }

  img {
    max-width: 100%;
    height: auto;
  }

  hr {
    margin: 1rem 0;
  }

  blockquote {
    padding-left: 1rem;
    border-left: 2px solid rgba(#0d0d0d, 0.1);
  }

  hr {
    border: none;
    border-top: 2px solid rgba(#0d0d0d, 0.1);
    margin: 2rem 0;
  }

  ul[data-type="taskList"] {
    list-style: none;
    padding: 0;

    li {
      display: flex;
      align-items: center;

      > label {
        flex: 0 0 auto;
        margin-right: 0.5rem;
        user-select: none;
      }

      > div {
        flex: 1 1 auto;
      }
    }
  }
}

/* Give a remote user a caret */
.collaboration-cursor__caret {
  position: relative;
  margin-left: -1px;
  margin-right: -1px;
  border-left: 1px solid #0d0d0d;
  border-right: 1px solid #0d0d0d;
  word-break: normal;
  pointer-events: none;
}

/* Render the username above the caret */
.collaboration-cursor__label {
  position: absolute;
  top: -1.4em;
  left: -1px;
  font-size: 12px;
  font-style: normal;
  font-weight: 600;
  line-height: normal;
  user-select: none;
  color: #0d0d0d;
  padding: 0.1rem 0.3rem;
  border-radius: 3px 3px 3px 0;
  white-space: nowrap;
}
</style>
