<script setup lang="ts">
import { nextTick, ref } from 'vue'
import { watch } from 'vue'
import { useFocusTrap } from '@vueuse/integrations/useFocusTrap'
import { type Card } from '@/types'

const props = defineProps<{
  isOpen: boolean
  card: Card | null
  mode: 'add' | 'edit'
}>()
const emit = defineEmits<{
  (e: 'close'): void
  (e: 'save', card: Card): void
}>()
const titleInput = ref<HTMLInputElement | null>(null)
const modalElement = ref<HTMLElement | null>(null)
const localCard = ref<Card>({
  id: 0,
  title: '',
  description: '',
})
const { activate, deactivate } = useFocusTrap(modalElement)

watch(
  () => props.card,
  (newCard) => {
    if (newCard) {
      localCard.value = { ...newCard }
    } else {
      localCard.value = { id: 0, title: '', description: '' }
    }
  },
  { immediate: true },
)

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
</script>

<template>
  <div
    v-if="isOpen"
    @keydown.esc="emit('close')"
    class="fixed inset-0 bg-black bg-opacity-50 flex items-center justify-center"
    role="dialog"
    aria-modal="true"
    ref="modalElement"
    @click.self="emit('close')"
  >
    <div class="bg-white p-5 rounded max-w-md w-full">
      <h2 class="text-xl font-bold mb-4">{{ mode === 'add' ? 'Add new Card' : 'Edit Card' }}</h2>
      <input
        v-model="localCard.title"
        type="text"
        placeholder="Card Title"
        arial-label="Card Title"
        class="w-full p-2 mb-4 border rounder"
        ref="titleInput"
      />
      <textarea
        v-model="localCard.description"
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
          @click="emit('save', localCard)"
          class="bg-blue-500 hover:bg-blue-600 text-white px-4 py-2 rounded"
        >
          {{ mode === 'add' ? 'Add' : 'Save' }}
        </button>
      </div>
    </div>
  </div>
</template>
