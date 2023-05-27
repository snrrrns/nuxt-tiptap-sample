<template>
  <div>
    <client-only>
      <tiptap-editor />
    </client-only>
  </div>
</template>

<script lang="ts">
import TiptapEditor from '~/components/TiptapEditor.vue'

export default {
  components: {
    TiptapEditor
  }
}
</script>
