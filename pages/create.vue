<template>
    <div class="w-full max-w-xs">
        <ul v-if="errors.length > 0" class="mb-4 list-disc list-inside text-sm text-red-600">        
            <li v-for="(error, index) in errors" :key="index">
                {{ error }}
            </li>
        </ul>
        <form @submit.prevent="createPost" class="bg-white shadow-md rounded px-8 pt-6 pb-8 mb-4">
            <div class="mb-4">
                <label class="block text-gray-700 text-sm font-bold mb-2" for="title">
                    Title
                </label>
                <input v-model="title" class="shadow appearance-none border rounded w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:shadow-outline" id="title" type="text" placeholder="Title">
            </div>
            <div class="mb-6">
                <label class="block text-gray-700 text-sm font-bold mb-2" for="body">
                    Body
                </label>
                <input v-model="body" class="shadow appearance-none border border-red-500 rounded w-full py-2 px-3 text-gray-700 mb-3 leading-tight focus:outline-none focus:shadow-outline" id="body" type="text" placeholder="Body">
            </div>
            <div class="flex items-center justify-between">
                <button type="submit" class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded focus:outline-none focus:shadow-outline">
                    Create
                </button>
                <span v-show="isLoading">Loading ... </span>
            </div>
        </form>
    </div>
</template>

<script setup>
definePageMeta({
    middleware: ["auth"]
})

const title = ref('')
const body = ref('')
const isLoading = ref(false)
const errors = ref([])
const router = useRouter()

async function createPost() {
    isLoading.value = true
    try {
        const post = await useNuxtApp().$apiFetch(`/api/posts`, {
            method: 'POST',
            body: {
                title: title.value,
                body: body.value,
            }
        });

        isLoading.value = false

        title.value = ''
        body.value = ''


        alert('creating post')

        router.push('/')
    } catch (err) {
        errors.value = Object.values(err.data.errors).flat()
        isLoading.value = false
    }
}

</script>