
<script setup>
import { computed, ref } from 'vue'
import { useProductsStore } from '@/stores/products'
import CardComponent from '@/components/CardComponent.vue'
import FilterComponent from '@/components/FilterComponent.vue'

const storeProducts = useProductsStore()

const categoriasMap = {
  1: 'florais',
  3: 'citricos',
  2: 'doces',
  4: 'lembrancinhas'
}

const aromasPorProduto = {
  'Bergamota': 'bergamota',
  'Capim Limão': 'capim-limao',
  'Flor de Laranjeira': 'flor-de-laranjeira',
  'Jasmim': 'jasmim',
  'Lavanda Francesa': 'lavanda-francesa',
  'Limão Siciliano': 'limao-siciliano',
  'Canela': 'canela',
  'Baunilha': 'baunilha',
  'Flor de Cerejeira': 'flor-de-cerejeira',
  'Maçã e Canela': 'maca-e-canela'
}

const precosMap = computed(() => {
  const mapa = {}
  storeProducts.productVariations.forEach(v => {
    if (!mapa[v.produto]) mapa[v.produto] = []
    mapa[v.produto].push(Number(v.preco))
  })
  return mapa
})

const productsAgrupadas = computed(() => {
  const mapa = {}

  storeProducts.products.forEach(produto => {
    const precos = precosMap.value[produto.id] || []

    if (!mapa[produto.nome]) {
      mapa[produto.nome] = {
        id: produto.id,
        nome: produto.nome,
        imagem: produto.imagem,
        categoriaIds: produto.categorias || [],
        categoria: produto.categorias
          ?.map(id => categoriasMap[id])
          .filter(Boolean) || [],
        precos: precos,
        aroma: aromasPorProduto[produto.nome],
        variacoes: storeProducts.productVariations.filter(
          v => String(v.produto) === String(produto.id)
        )
      }
    }
  })

  return Object.values(mapa).map(item => {
    const menor = item.precos.length ? Math.min(...item.precos) : 0
    const maior = item.precos.length ? Math.max(...item.precos) : 0

    return {
      ...item,
      precoMin: menor,
      precoMax: maior
    }
  })
})

const filtros = ref({
  ordenacao: '',
  colecoes: []
})

const aplicarFiltros = (dados) => {
  filtros.value = dados
}

const limparFiltros = () => {
  filtros.value = {
    ordenacao: '',
    colecoes: []
  }
}

const productsFiltradas = computed(() => {
  let lista = [...productsAgrupadas.value]

  if (filtros.value.colecoes.length > 0) {
    lista = lista.filter(product =>
      product.categoriaIds.some(id =>
        filtros.value.colecoes.includes(String(id))
      )
    )
  }

  if (filtros.value.ordenacao === 'maior') {
    lista.sort((a, b) => b.precoMax - a.precoMax)
  }

  if (filtros.value.ordenacao === 'menor') {
    lista.sort((a, b) => a.precoMin - b.precoMin)
  }

  if (filtros.value.ordenacao === 'alfabetica') {
    lista.sort((a, b) => a.nome.localeCompare(b.nome))
  }

  if (filtros.value.ordenacao === 'decrescente') {
    lista.sort((a, b) => b.nome.localeCompare(a.nome))
  }

  return lista
})
</script>

<template>
  <FilterComponent @aplicar="aplicarFiltros" @limpar="limparFiltros" />
  <p v-if="storeProducts.loading">Carregando...</p>
  <p v-else-if="storeProducts.error">{{ storeProducts.error }}</p>
  <div v-else-if="productsFiltradas.length > 0" class="grid grid-cols-2 m-4 sm:grid-cols-3 sm:m-6 md:grid-cols-4 md:m-10 lg:m-20 lg:gap-4 lg:grid-cols-5">
    <CardComponent v-for="product in productsFiltradas" :key="product.nome" :product="product" />
  </div>
</template>
