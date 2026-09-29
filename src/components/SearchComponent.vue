<script setup>
import { ref, computed, onMounted } from 'vue'
import { useRouter } from 'vue-router'
import { useProductsStore } from '@/stores/products'

const router = useRouter()
const productsStore = useProductsStore()

const busca = ref('')

onMounted(async () => {
    if (!productsStore.products?.length) {
        await productsStore.fetchProducts()
    }
})

const resultados = computed(() => {
    const termo = busca.value.trim().toLowerCase()

    if (!termo) return []

    return productsStore.products.filter(product =>
        String(product.nome ?? '').toLowerCase().includes(termo)
    )
})

function selecionarProduto(product) {
    busca.value = ''

    router.push({
        name: 'produto',
        params: {
            id: product.id
        }
    })
}
</script>
<template>
    <div class="relative">
        <div class="flex items-center gap-2 border-b border-white/70 px-1 py-1">
            <img src="/icons/buscar.svg" alt="Buscar" class="w-5 md:w-6">
            <input v-model="busca" type="text" placeholder="Buscar produtos..."
                class="w-28 md:w-40 lg:w-52 bg-transparent text-white placeholder-white/70 outline-none text-sm">
        </div>
        <div v-if="busca.trim()"
            class="absolute right-0 top-full mt-3 w-72 bg-white border border-[#E7EAE9] shadow-lg z-[100]">
            <button v-for="product in resultados" :key="product.id" @click="selecionarProduto(product)"
                class="w-full flex items-center gap-3 p-3 text-left hover:bg-[#F5F5F5] transition">
                <img v-if="product.imagem?.url" :src="product.imagem.url" :alt="product.nome"
                    class="w-12 h-12 object-cover">
                <div>
                    <p class="text-sm text-[#0C2645] font-semibold">{{ product.nome }} </p>
                    <p class="text-xs text-[#2C2828]"> Vela Aromática</p>
                </div>
            </button>
            <p v-if="resultados.length === 0" class="p-4 text-sm text-gray-500 text-center">Nenhum produto encontrado.
            </p>
        </div>
    </div>
</template>