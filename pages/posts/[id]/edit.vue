<template>
    <div class="w-full max-w-xs">
        <ul v-if="errors.length > 0" class="mb-4 list-disc list-inside text-sm text-red-600">        
            <li v-for="(error, index) in errors" :key="index">
                {{ error }}
            </li>
        </ul>
        <h2>Edit Post</h2>
        <form @submit.prevent="updatePost" class="bg-white shadow-md rounded px-8 pt-6 pb-8 mb-4">
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
                    Update
                </button>
                <span v-show="isLoading">Loading ... </span>
            </div>
        </form>
        <div class="mt-4">
            <button @click="deletePost">Delete Post</button>
        </div>
    </div>
</template>

<script setup>
//definePageMeta({
//    middleware: ["auth"]
//})

const title = ref('')
const body = ref('')
const isLoading = ref(false)
const errors = ref([])
const router = useRouter()
const route = useRoute()
const { $apiFetch } = useNuxtApp()
const post = ref(null)

onMounted(async () => {
    try {
        post.value = await $apiFetch(`/api/postsAuth/${route.params.id}`);
        title.value = post.value.title
        body.value = post.value.body
    } catch (err) {
        window.location.pathname = '/'
    }
})

async function updatePost() {
    isLoading.value = true
    try {
        const post = await $apiFetch(`/api/posts/${route.params.id}`, {
            method: 'PATCH',
            body: {
                title: title.value,
                body: body.value,
            }
        });

        isLoading.value = false

        title.value = ''
        body.value = ''


        alert('updated post')

        router.push('/my-info')
    } catch (err) {
        if (err.response.status === 403) {
            alert(err.data.message)
            return
        }
        errors.value = Object.values(err.data.errors).flat()
        isLoading.value = false
    }
}

async function deletePost() {
    isLoading.value = true
    try {
        const post = await $apiFetch(`/api/posts/${route.params.id}`, {
            method: 'DELETE',
        });

        isLoading.value = false

        title.value = ''
        body.value = ''


        alert('deleted post')

        router.push('/my-info')
    } catch (err) {
        isLoading.value = false
    }
}

</script>