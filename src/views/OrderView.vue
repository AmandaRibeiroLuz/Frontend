<script setup>
import { computed, ref } from 'vue'
import { useRouter } from 'vue-router'
import { useBagStore } from '@/stores/bag.js'

const router = useRouter()
const bagStore = useBagStore()

const items = computed(() => bagStore.items)

const metodoPagamento = ref(null)

function formatPrice(value) {
    return Number(value).toLocaleString('pt-BR', {
        minimumFractionDigits: 2,
        maximumFractionDigits: 2
    })
}

function selecionarPagamento(metodo) {
    metodoPagamento.value = metodo
}

function voltar() {
    router.push('/sacola')
}

function fazerPedido() {
    if (!metodoPagamento.value) {
        return
    }
    console.log('Pedido:', {
        itens: items.value,
        pagamento: metodoPagamento.value,
        total: bagStore.total
    })
}
</script>
<template>
    <main class="min-h-screen">
        <section class="mx-auto w-[calc(100%-48px)] max-w-[1250px] pt-10 pb-10 md:pt-[190px] md:pb-16  lg:grid lg:grid-cols-[1fr_1fr] lg:gap-[110px]">
            <div>
                <h1 class="mb-12 text-center font-[Cinzel] text-3xl text-[#0C2645] md:text-left md:text-4xl lg:text-4xl">
                    FAZER PEDIDO </h1>
                <div class="border-t border-[#BFC0C0]pt-6 md:pt-0 md:border-t-0">
                    <div v-for="item in items" :key="item.id" class="mb-3 flex h-[140px] items-center  border border-[#BFC0C0] p-2  md:h-[155px]">
                        <div class="h-[120px] w-[118px] shrink-0  md:h-[135px] md:w-[132px]">
                            <img :src="item.imagem" :alt="`Vela Aromática - ${item.nome}`"  class="h-full w-full object-cover" />
                        </div>
                        <div class="ml-3 flex h-full flex-1  flex-col justify-between py-1">
                            <div>
                                <p class="text-[14px] leading-[17px]  text-[#2C2828]"> Vela Aromática - </p>
                                <p class="text-[14px] leading-[17px] text-[#2C2828]"> {{ item.nome }}
                                </p>
                                <p class="text-[13px]  text-[#2C2828]"> Tamanho: {{ item.tamanho }} </p>
                            </div>
                            <p class="text-[21px]   text-[#2C2828]"> R${{ formatPrice(item.preco) }} </p>
                        </div>
                    </div>
                    <div v-if="items.length === 0" class="py-10 text-center text-[#2C2828]"> Sua sacola está vazia.
                    </div>
                </div>
            </div>
            <div class="mt-12 lg:mt-0">
                <section>
                    <h2 class="mb-4 font-[Cinzel] text-2xl text-[#2C2828] md:text-3xl"> MÉTODO DE PAGAMENTO </h2>
                    <button type="button" @click="selecionarPagamento('pix')" class="flex w-full items-center border-b border-[#BFC0C0]  py-2 text-left" :class="{ 'bg-[#F7F7F7]': metodoPagamento === 'pix' }">
                        <span class="flex w-12 shrink-0 items-center justify-center">
                            <img src="/icons/pix.svg" alt="Pix" class="w-6 h-6">
                        </span>
                        <span class="flex-1 text-base text-[#2C2828]"> Pix</span>
                        <span class="mr-2 text-2xl font-light  text-[#BFC0C0]">› </span>
                    </button>
                    <button type="button" @click="selecionarPagamento('dinheiro')" class="flex w-full items-center border-b border-[#BFC0C0] py-2 text-left" :class="{ 'bg-[#F7F7F7]': metodoPagamento === 'dinheiro' }">
                        <span class="flex w-12 shrink-0  items-center justify-center"> </span>
                        <img src="/icons/dinheiro.svg" alt="Dinheiro" class="w-6 h-6">
                        <span class="flex-1 text-base   text-[#2C2828]"> Dinheiro (Combinar com a vendedora) </span>
                        <span class="mr-2 text-2xl font-light text-[#BFC0C0]"> › </span>
                    </button>
                </section>
                <section class="mt-20">
                    <h2 class="mb-4 font-[Cinzel] text-2xl text-[#2C2828]"> ENTREGA </h2>
                    <button type="button" class="underline underline-offset-2"> Combine a entrega com o vendedor
                        clicando aqui. </button>
                    <p class="mt-1 text-base leading-8 text-[#2C2828]"> Ou, combine após finalizar o pedido.</p>
                </section>
                <section class="mt-14">
                    <div class="border-t border-[#BFC0C0]">
                        <div class="flex justify-between border-b border-[#BFC0C0] px-1 py-2 text-sm text-[#2C2828]">
                            <span>Subtotal:</span>
                            <span> R${{ formatPrice(bagStore.subtotal) }} </span>
                        </div>
                        <div class="flex justify-between border-b border-[#BFC0C0]  px-1 py-2 text-sm text-[#2C2828]">
                            <span>Descontos:</span>
                            <span> R${{ formatPrice(bagStore.descontos) }} </span>
                        </div>
                        <div class="flex justify-between px-1 py-1 text-xl text-[#0C2645]">
                            <span>Total</span>
                            <span> R${{ formatPrice(bagStore.total) }}</span>
                        </div>
                    </div>
                </section>
                <div class="mt-8 flex gap-4">
                    <button type="button" @click="voltar" class="h-[40px] flex-1 border border-[#0C2645] text-[#0C2645] transition hover:bg-gray-100">
                        Voltar </button>
                    <button type="button" @click="fazerPedido" class="h-[40px] flex-1  bg-[#0C2645] text-white transition hover:bg-[#163657]"> Fazer
                        Pedido</button>
                </div>
            </div>
        </section>
    </main>
</template>