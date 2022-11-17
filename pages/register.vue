<template>
    <div class="w-full max-w-xs">
        <Title>Register | {{ title }}</Title>
        <ul v-if="errors.length > 0" class="mb-4 list-disc list-inside text-sm text-red-600">
            <li v-for="(error, index) in errors" :key="index">
                {{ error }}
            </li>
        </ul>
        <form @submit.prevent="register" class="bg-white shadow-md rounded px-8 pt-6 pb-8 mb-4">
            <div class="mb-4">
                <label class="block text-gray-700 text-sm font-bold mb-2" for="name">
                    Name
                </label>
                <input v-model="name" class="shadow appearance-none border rounded w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:shadow-outline" type="text" placeholder="name">
            </div>
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
            <div class="mb-6">
                <label class="block text-gray-700 text-sm font-bold mb-2" for="passwordConfirm">
                    Password Confirmation
                </label>
                <input v-model="passwordConfirm" class="shadow appearance-none border border-red-500 rounded w-full py-2 px-3 text-gray-700 mb-3 leading-tight focus:outline-none focus:shadow-outline" type="password" placeholder="password confirm">
            </div>
            <div class="flex items-center justify-between">
                <button type="submit" class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded focus:outline-none focus:shadow-outline">
                    Register
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

const name = ref('')
const email = ref('')
const password = ref('')
const passwordConfirm = ref('')
const isLoading = ref(false)
const errors = ref([])

const { $apiFetch } = useNuxtApp()

function csrf() {
    return $apiFetch('/sanctum/csrf-cookie')
}

async function register() {
    await csrf()
    isLoading.value = true;

    try {
        await $apiFetch('/register', {
            method: 'POST',
            body: {
                name: name.value,
                email: email.value,
                password: password.value,
                password_confirmation: passwordConfirm.value,
            },
        });
        const user = await $apiFetch('/api/user')
        const { setUser } = useAuth()
        setUser(user.name)

        alert('registered')
        //router.push('/my-info')
        window.location.pathname = '/my-info'
    } catch (err) {
        console.log(err.data)
        errors.value = Object.values(err.data.errors).flat()
    }
    isLoading.value = false
}
</script>