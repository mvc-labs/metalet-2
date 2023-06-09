<script setup lang="ts">
import { useRoute, useRouter } from 'vue-router'
import { computed, ref, Ref, inject, onMounted } from 'vue'
import { getNetwork } from './lib/network'
import { API_NET, API_TARGET, Wallet } from 'meta-contract'

import TheFooter from './components/the-footer/Index.vue'
import TheHeader from './components/headers/TheHeader.vue'
import SecondaryHeader from './components/headers/SecondaryHeader.vue'
import accountManager, { getCurrentAccount } from './lib/account'
import type { Account } from './lib/account'
import { FEEB } from './data/config'
import BgHueImg from './assets/images/bg-hue.png?url'

const router = useRouter()
const route = useRoute()

// 从storage读取助记词
const network = ref('testnet')
getNetwork().then((net) => {
  network.value = net
})
const wallet: Ref<any> = inject('wallet')!
getCurrentAccount().then(async (account) => {
  if (account) {
    const wif =
      network.value === 'mainnet' ? account.mainnetPrivateKey.toString() : account.testnetPrivateKey.toString()

    wallet.value = new Wallet(wif, network.value as API_NET, FEEB, API_TARGET.MVC)
  }
})

const noFooter = computed(() => {
  return route.meta.noFooter
})

const secondaryHeaderTitle = computed(() => {
  return route.meta.headerTitle
})
</script>

<template>
  <div
    id="app"
    class="ext-app relative flex h-150 w-90 items-center justify-center overflow-y-auto sm:h-screen sm:w-screen sm:bg-gray-200/10"
  >
    <div
      class="ext-app flex h-full w-full flex-col overflow-y-auto sm:relative sm:aspect-[1/2] sm:h-3/4 sm:w-auto sm:min-w-[25rem] sm:rounded-lg sm:border sm:border-gray-100 sm:bg-white sm:shadow-lg"
    >
      <!-- Header -->
      <SecondaryHeader v-if="route.meta.secondaryHeader">
        <template #title>
          {{ secondaryHeaderTitle }}
        </template>
      </SecondaryHeader>

      <TheHeader v-else />

      <!-- bg -->
      <div
        class="bg-hue fixed top-0 left-0 isolate z-[-1] hidden h-1/2 w-full select-none bg-cover bg-center bg-no-repeat sm:block"
      >
        <img class="z-[-1] h-full w-full select-none opacity-100" :src="BgHueImg" alt="bg-hue" />
      </div>
      <div class="isolate grow px-5" :class="noFooter ? 'pb-8' : 'pb-16'">
        <router-view></router-view>
      </div>

      <!-- footer -->
      <TheFooter v-if="!noFooter" />
    </div>
  </div>
</template>

<style scoped lang="css">
/* #app:after {
  background: linear-gradient(transparent, #3b82f6, transparent);
  height: 140px;
  width: 3px;
  content: '';
  position: absolute;
  left: -1px;
  top: 70%;
  opacity: 0;
  transition: top 600ms ease, opacity 600ms ease;
}

#app:hover:after {
  opacity: 1;
  top: 25%;
} */

.ext-app::-webkit-scrollbar {
  @apply h-1.5 w-1.5;
}

.ext-app::-webkit-scrollbar-track {
  background: transparent;
  @apply rounded-full bg-transparent;
}

.ext-app::-webkit-scrollbar-thumb {
  @apply rounded-full bg-gray-200;
}

.ext-app::-webkit-scrollbar-thumb:hover {
  @apply bg-gray-300;
}
</style>
