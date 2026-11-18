<script setup>
import { ref } from 'vue'
import { useUserStore } from '@/stores/userInfoStore'
import { useAuthStore } from '@/stores/auth'
import authApi from '@/api/authApi'

const props = defineProps({
  user: {
    type: Object,
    required: true
  }
})

const emit = defineEmits(['saved', 'cancel'])
const authStore = useAuthStore()
const userStore = useUserStore()
const salvando = ref(false)
const erro = ref('')
const form = ref({
  name: '',
  email: '',
  telefone: '',
  nascimento: ''
})

const fotoPreview = ref(null)
const fotoAttachmentKey = ref(null)
const uploadingFoto = ref(false)

function iniciarEdicao() {
  form.value = {
    name: props.user.name || '',
    email: props.user.email || '',
    telefone: props.user.telefone || '',
    nascimento: props.user.nascimento || ''
  }
  fotoPreview.value = null
  fotoAttachmentKey.value = null
  erro.value = ''
}

function voltarAoTopo() {
  window.scrollTo({
    top: 0,
    behavior: 'smooth'
  })
}

function cancelarEdicao() {
  fotoPreview.value = null
  fotoAttachmentKey.value = null
  erro.value = ''

  emit('cancel')
  voltarAoTopo()
}

async function handleFotoChange(event) {
  const file = event.target.files[0]
  if (!file) return
  fotoPreview.value = URL.createObjectURL(file)
  uploadingFoto.value = true
  erro.value = ''

  try {
    const response = await authApi.uploadImage(file)
    fotoAttachmentKey.value = response.data.attachment_key
  } catch (err) {
    console.error('Erro ao fazer upload da foto:', err)
    fotoPreview.value = null
    fotoAttachmentKey.value = null
    erro.value = 'Não foi possível enviar a foto.'
  } finally {
    uploadingFoto.value = false
  }
}

async function salvarPerfil() {
  erro.value = ''
  if (!form.value.name.trim()) {
    erro.value = 'O nome não pode ficar vazio.'
    return
  }
  salvando.value = true
  try {
    const payload = {
      name: form.value.name.trim(),
      email: form.value.email.trim(),
      telefone: form.value.telefone.trim(),
      nascimento: form.value.nascimento
    }
    if (fotoAttachmentKey.value) {
      payload.foto_attachment_key = fotoAttachmentKey.value
    }
    const usuarioAtualizado = await authStore.updateProfile(
      props.user.id,
      payload
    )
    userStore.user = {
      id: usuarioAtualizado.id,
      name: usuarioAtualizado.name,
      email: usuarioAtualizado.email,
      telefone: usuarioAtualizado.telefone,
      nascimento: usuarioAtualizado.nascimento,
      foto: usuarioAtualizado.foto?.url || ''
    }
    fotoPreview.value = null
    fotoAttachmentKey.value = null
    emit('saved')
    voltarAoTopo()
  } catch (err) {
    console.error('Erro ao atualizar perfil:', err)
    if (err.response?.data) {
      console.error('Resposta do servidor:', err.response.data)
    }
    erro.value = 'Não foi possível atualizar o perfil.'
  } finally {
    salvando.value = false
  }
}

iniciarEdicao()
</script>
<template>
  <div class="mt-6 border border-gray-300 bg-white p-5">
    <h2 class="text-[#0C2645] font-serif text-lg mb-4"> EDITAR PERFIL </h2>
    <div v-if="erro" class="mb-4 p-3 border border-red-300 bg-red-50 text-red-700 text-sm">  {{ erro }}</div>
    <div class="grid gap-4">
      <div><label class="block text-sm mb-1">Nome</label><input v-model="form.name" type="text" class="w-full border border-gray-300 p-2"></div>
      <div><label class="block text-sm mb-1">E-mail</label><input v-model="form.email" type="email" class="w-full border border-gray-300 p-2"></div>
      <div><label class="block text-sm mb-1">Telefone</label><input v-model="form.telefone" type="text" class="w-full border border-gray-300 p-2"></div>
      <div><label class="block text-sm mb-1">Data de nascimento</label> <input v-model="form.nascimento" type="text" placeholder="DD/MM/AAAA" class="w-full border border-gray-300 p-2"></div>
      <div>
        <label class="block text-sm mb-2">Foto</label>
        <div v-if="fotoPreview || user.foto" class="mb-3 flex items-center gap-3">
          <img :src="fotoPreview || user.foto" alt="Preview da foto" class="w-[90px] h-[90px] object-cover border border-gray-300">
          <span class="text-sm text-gray-500"> {{ fotoPreview ? 'Nova foto selecionada' : 'Foto atual' }} </span>
        </div>
        <label class="inline-block border border-[#0C2645] px-4 py-2 cursor-pointer text-[#0C2645]" :class="{ 'opacity-50 cursor-not-allowed': uploadingFoto }"> {{ uploadingFoto ? 'Enviando...' : 'Alterar foto' }}
          <input type="file" accept="image/jpeg,image/png" class="hidden" :disabled="uploadingFoto" @change="handleFotoChange">
        </label>
      </div>
      <div class="flex gap-2 mt-2">
        <button @click="salvarPerfil" :disabled="salvando || uploadingFoto" class="bg-[#0C2645] text-white px-5 py-2 disabled:opacity-50"> {{ salvando ? 'Salvando...' : 'Salvar alterações' }} </button>
        <button @click="cancelarEdicao" :disabled="salvando" class="border border-gray-300 px-5 py-2"> Cancelar </button>
      </div>
    </div>
  </div>
</template>