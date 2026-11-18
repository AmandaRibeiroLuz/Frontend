
<script setup>
import { onMounted, ref } from 'vue'
import { useRoute, useRouter } from 'vue-router'
import Banner from '@/components/BannerComponent.vue'
import { useBannerStore } from '@/stores/useBannerStore'
import InstallButton from '../components/InstallButton.vue'
import ProductList from '@/components/ProductList.vue'

const route = useRoute()
const router = useRouter()
const store = useBannerStore()
const showLoginMessage = ref(false)

onMounted(() => {
  if (route.query.login === 'success') {
    showLoginMessage.value = true

    setTimeout(() => {
      showLoginMessage.value = false
    }, 4000)

    router.replace({
      path: route.path,
      query: {}
    })
  }
})

</script>

<template>
  <Banner :imagens="store.getBanners('home')" />

  <Transition name="fade">
    <div v-if="showLoginMessage" class="top-5 md:bottom-auto md:top-30 left-1/2 -translate-x-1/2 fixed z-[110] flex items-center gap-2 rounded-lg border border-green-200 bg-green-50 px-3 py-3 text-green-700 shadow-lg" >
      <span class="text-lg">✓</span>
      <span>Login realizado com sucesso! Seja bem-vindo(a).</span>
    </div>
  </Transition>
  <ProductList />
  <InstallButton />
</template>

<style scoped>
.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.5s;
}

.fade-enter-from,
.fade-leave-to {
  opacity: 0;
}
</style>