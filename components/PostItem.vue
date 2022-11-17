<template>
    <div class="post-container py-8">
        <h3 class="text-3xl font-bold">
            <NuxtLink :to="`/posts/${props.post.id}`">{{ props.post.title }}</NuxtLink>
            <!--<NuxtLink :to="`/posts/1`">Title</NuxtLink>-->
        </h3>
        <div class="post-meta text-gray-700 flex items-center space-x-2">
            <div>{{ format(new Date(props.post.created_at), 'MM/dd/yyyy') }}</div>
            <div>&middot;</div>
            <div>{{ props.post.name }}</div>
        </div>
        <div class="post-preview leading-relaxed mt-4 line-clamp-3">
            {{ props.post.body }}
        </div>
        <div class="mt-4">
            <NuxtLink :to="`/posts/${props.post.id}`" class="bg-blue-700 hover:bg-blue-800 text-white rounded inline-block px-4 py-2">Read More</NuxtLink>
            <!--<NuxtLink :to="`/posts/1`" class="bg-blue-700 hover:bg-blue-800 text-white rounded inline-block px-4 py-2">Read More</NuxtLink>-->
        </div>
    </div>
</template>

<script setup>
import { format } from 'date-fns'

const props = defineProps({
    post: Object,
});

</script>