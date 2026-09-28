<script setup>
import { ref } from 'vue'
import { RouterLink } from 'vue-router';
import { useAuthStore } from '@/stores/auth'
import { useRouter } from 'vue-router'
import SearchComponent from '@/components/SearchComponent.vue'

const authStore = useAuthStore()
const router = useRouter()

function handleLogout() {
    authStore.logout();
    router.push('/login');
}
</script>
<template>
    <header class="hidden md:block fixed z-50 top-0 left-0 w-full bg-[#0C2645] p-5">
        <ul class="flex gap-5 items-center justify-between sm:gap-20 md:gap-10">
            <li>
                <ul class="flex items-center gap-5">
                    <li class="w-20 mb-2 xl:mr-8 lg:ml-8">
                        <RouterLink to="/">
                            <img src="/img/logo.svg" alt="Lumena">
                        </RouterLink>
                    </li>
                    <li>
                       <SearchComponent />
                    </li>
                </ul>
            </li>
            <li>
                <ul class="flex items-center gap-5 xl:gap-15 xl:mr-10">
                    <li>
                        <RouterLink to="/">
                            <span class="text-lg text-white hover:font-bold">Início</span>
                        </RouterLink>
                    </li>
                    <li>
                        <RouterLink to="/aromas">
                            <span class="text-lg text-white hover:font-bold">Aromas</span>
                        </RouterLink>
                    </li>
                    <li>
                        <RouterLink to="/perfil">
                            <span class="text-lg text-white hover:font-bold">Perfil</span>
                        </RouterLink>
                    </li>
                    <li>
                        <RouterLink to="/sacola">
                            <span class="text-lg text-white hover:font-bold">Sacola</span>
                        </RouterLink>
                    </li>
                    <li>
                        <button v-if="authStore.isAuthenticated" @click="handleLogout"
                            class="flex flex-col items-center justify-center text-white font-sen w-6 md:w-8">
                            <span class="text-lg text-white hover:font-bold">Sair</span>
                        </button>
                    </li>
                </ul>
            </li>
        </ul>
    </header>
    <div class="md:hidden fixed bottom-0 z-50 w-full h-15 bg-[#0C2645] rounded-t-2xl">
        <ul class="h-full flex items-center justify-center">
            <li class="h-full w-30">
                <RouterLink to="/aromas" class="h-full w-full flex flex-col items-center justify-center text-white"
                    active-class="text-[#FDA202]">
                    <img class="w-5 h-5" src="/icons/aromas.png" alt="Aroma">
                    <span class="text-sm font-sen">Aromas</span>
                </RouterLink>
            </li>
            <li class="h-full w-30">
                <RouterLink to="/sacola" class="h-full w-full flex flex-col items-center justify-center text-white"
                    active-class="text-[#FDA202]">
                    <img class="w-5 h-5" src="/icons/sacola.svg" alt="Sacola">
                    <span class="text-sm font-sen">Sacola </span>
                </RouterLink>
            </li>
            <li class="h-full w-30">
                <RouterLink to="/" class="h-full w-full flex flex-col items-center justify-center text-white"
                    active-class="text-[#FDA202]">
                    <img  class="w-5 h-5" src="/icons/home.svg" alt="Início">
                    <span class="text-sm font-sen">Início</span>
                </RouterLink>
            </li>
            <li class="h-full w-30">
                <RouterLink :to="authStore.isAuthenticated ? '/perfil' : '/login'"
                    class="h-full w-full flex flex-col items-center justify-center text-white"
                    active-class="text-[#FDA202]">
                    <img  class="w-5 h-5" src="/icons/usuario.svg" alt="Perfil">
                    <span class="text-sm font-sen"> Perfil</span>
                </RouterLink>
            </li> 
            <button v-if="authStore.isAuthenticated" @click="handleLogout"
                    class="h-full w-30 flex flex-col items-center justify-center text-white">
                    <img class="w-5 h-5" src="/icons/user-logout-white.svg" alt="Logout">
                    <span class="text-sm font-sen">Sair</span>
            </button>
        </ul>
    </div>
</template>