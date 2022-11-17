<template>
    <div class="w-full max-w-xs">
        <Title>Login | {{ title }}</Title>
        <ul v-if="errors.length > 0" class="mb-4 list-disc list-inside text-sm text-red-600">
            <li v-for="(error, index) in errors" :key="index">
                {{ error }}
            </li>
        </ul>
        <form @submit.prevent="login" class="bg-white shadow-md rounded px-8 pt-6 pb-8 mb-4">
            <div class="mb-4">
                <label class="block text-gray-700 text-sm font-bold mb-2" for="email">
                    Email
                </label>
                <input v-model="email" class="shadow appearance-none border rounded w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:shadow-outline" type="text" placeholder="email">
            </div>
            <div class="mb-6">
                <label class="block text-gray-700 text-sm font-bold mb-2" for="password">
                    Password
                </label>
                <input v-model="password" class="shadow appearance-none border border-red-500 rounded w-full py-2 px-3 text-gray-700 mb-3 leading-tight focus:outline-none focus:shadow-outline" type="password" placeholder="password">
            </div>
            <div class="flex items-center justify-between">
                <button type="submit" class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded focus:outline-none focus:shadow-outline">
                    Login
                </button>
                <span v-show="isLoading">Loading ... </span>
            </div>
        </form>
    </div>
</template>

<script setup>
definePageMeta({
    middleware: ['guest']
})

const title = useState('title')

const email = ref('')
const password = ref('')
const isLoading = ref(false)
const errors = ref([])

const { $apiFetch } = useNuxtApp()

function csrf() {
    return $apiFetch('/sanctum/csrf-cookie')
}

async function login() {
    isLoading.value = true;

    await csrf()

    try {
        await $apiFetch('/login', {
            method: 'POST',
            body: {
                email: email.value,
                password: password.value,
            },
        });
        const user = await $apiFetch('/api/user')

        const { setUser } = useAuth()
        setUser(user.name)

        //router.push('/my-info')
        window.location.pathname = '/my-info'
    } catch (err) {
        errors.value = Object.values(err.data.errors).flat()
    }
    isLoading.value = false
}
</script>