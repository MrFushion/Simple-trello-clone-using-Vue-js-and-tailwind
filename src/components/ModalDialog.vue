<script setup lang="ts">
import { nextTick, ref } from 'vue'
import { defineEmits, watch } from 'vue'
import { useFocusTrap } from '@vueuse/integrations/useFocusTrap'

const props = defineProps<{
  isOpen: boolean
}>()
const emit = defineEmits<{
  (e: 'close'): void
}>()
const titleInput = ref<HTMLInputElement | null>(null)

watch(
  () => props.isOpen,
  async (isOpen) => {
    if (isOpen) {
      await nextTick()
      activate()
      titleInput.value?.focus()
    } else {
      deactivate()
    }
  },
)

const modalElement = ref<HTMLElement | null>(null)
const { activate, deactivate } = useFocusTrap(modalElement)
</script>

<template>
  <div
    v-if="isOpen"
    @keydown.esc="emit('close')"
    class="fixed inset-0 bg-black bg-opacity-50 flex items-center justify-center"
    role="dialog"
    aria-modal="true"
    ref="modalElement"
    @click="emit('close')"
  >
    <div class="bg-white p-5 rounded max-w-md w-full">
      <h2 class="text-xl font-bold mb-4">Add New Card</h2>
      <input
        type="text"
        placeholder="Card Title"
        arial-label="Card Title"
        class="w-full p-2 mb-4 border rounder"
        ref="titleInput"
      />
      <textarea
        class="w-full p-2 mb-4 border rounded"
        placeholder="Description"
        arial-label="Description"
      />

      <div class="flex justify-end gap-2">
        <button
          @click="emit('close')"
          class="bg-gray-300 hover:bg-gray-200 text-black px-4 py-2 rounded"
        >
          Cancel
        </button>
        <button
          @click="emit('close')"
          class="bg-blue-500 hover:bg-blue-600 text-white px-4 py-2 rounded"
        >
          Save
        </button>
      </div>
    </div>
  </div>
</template>
