<script lang="ts" setup>
import { ref, computed, Ref } from 'vue'
import { useRoute } from 'vue-router'

import { getAddress, address } from '../../lib/account'
import assets from '../../data/assets'

const route = useRoute()
const chain: Ref<'btc' | 'mvc'> = ref(route.query.chain as 'btc' | 'mvc')
const asset = computed(() => assets.find((asset) => asset.chain === chain.value))

const isCopied = ref(false)
function copyAddress() {
  navigator.clipboard.writeText(address.value)
  isCopied.value = true
}
</script>

<template>
  <div class="mt-8 flex flex-col items-center justify-center gap-y-3">
    <img :src="asset!.logo" alt="" class="h-16 w-16 rounded-xl" />

    <div class="mt-8 text-sm text-gray-500">
      Your <span class="uppercase">{{ chain }}</span> Address
    </div>
    <div class="main-input flex w-full items-center gap-x-4">
      <div class="truncate pl-2 text-sm">{{ address }}</div>
      <!-- copy button -->
      <button
        class="w-20 shrink-0 rounded-lg bg-gradient-to-r from-cyan-600 via-blue-600 to-indigo-600 py-3 text-xs font-bold text-sky-100 transition-all hover:saturate-150"
        :class="{ 'opacity-50 hover:!saturate-100': isCopied }"
        @click="copyAddress"
        :disabled="isCopied"
      >
        {{ isCopied ? 'Copied' : 'Copy' }}
      </button>
    </div>
  </div>
</template>
