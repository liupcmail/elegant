<template>
  <div class="editor-footer">
    <document-statistics
      v-if="wordCount"
      :pathname="pathname"
      :word-count="wordCount"
      :selection-word-count="selectionWordCount"
      :is-saved="isSaved"
      :read-only="!!currentFile?.readOnly"
      :mode-changing="!!currentFile?.modeChanging"
      @toggle-reading="setReadingMode"
    />
  </div>
</template>

<script setup lang="ts">
import { computed } from 'vue'
import { storeToRefs } from 'pinia'
import { useEditorStore } from '@/store/editor'
import DocumentStatistics from '../titleBar/documentStatistics.vue'

// Typora-style bottom-left status entry: the document statistics pill that
// used to live in the title bar, relocated to the editor footer.
const editorStore = useEditorStore()
const { currentFile } = storeToRefs(editorStore)

const pathname = computed(() => currentFile.value?.pathname)
const wordCount = computed(() => currentFile.value?.wordCount ?? null)
const selectionWordCount = computed(() => editorStore.selectionWordCount ?? null)
const isSaved = computed(() => currentFile.value?.isSaved ?? false)

const setReadingMode = (readOnly: boolean): void => {
  const file = currentFile.value
  if (file) void editorStore.SET_READ_ONLY(file.id, readOnly)
}
</script>

<style scoped>
.editor-footer {
  position: absolute;
  left: 12px;
  bottom: 6px;
  z-index: 100;
  /* The empty footer strip must not swallow editor clicks; the statistics
     pill inside re-enables pointer events for itself. */
  pointer-events: none;
}

.editor-footer :deep(.document-statistics-trigger) {
  pointer-events: auto;
  margin-right: 0;
}
</style>
