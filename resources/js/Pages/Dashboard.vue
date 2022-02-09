<template>
    <Head title="Dashboard"/>

    <BreezeAuthenticatedLayout>
        <template #header>
            <h2 class="font-semibold text-xl text-gray-800 leading-tight">
                Dashboard
            </h2>
        </template>

        <div class="py-12">
            <div class="max-w-7xl mx-auto sm:px-6 lg:px-8">
                <div class="bg-white overflow-hidden shadow-sm sm:rounded-lg">
                    <div class="p-6 bg-white border-b border-gray-200">
                        <form @submit.prevent="formSubmitHandler">
                            <input
                                class="appearance-none block w-full bg-gray-200 text-gray-700 border border-gray-200 rounded py-3 px-4 mb-3 leading-tight focus:outline-none focus:bg-white focus:border-gray-500"
                                id="search-posts" type="text" placeholder="Search posts" v-model="search">
                        </form>


                        <div v-if="posts.length > 0">
                            <div class="max-w-sm w-full lg:max-w-full lg:flex mb-3" v-for="(post, index) in posts"
                                 :key="index">
                                <div
                                    class="border-r border-b border-l border-gray-400 lg:border-l-0 lg:border-t lg:border-gray-400 bg-white rounded-b lg:rounded-b-none lg:rounded-r p-4 flex flex-col justify-between leading-normal">
                                    <div class="mb-8">
                                        <div class="text-gray-900 font-bold text-xl mb-2">
                                            {{ post.title }}
                                        </div>
                                        <p class="text-gray-700 text-base">
                                            {{ post.short_body }}
                                        </p>
                                    </div>
                                    <div class="flex items-end">
                                        <div class="text-sm">
                                            <p class="text-gray-900 leading-none">{{ post.user.name }}</p>
                                            <p class="text-gray-600">{{ post.user.email }}</p>
                                        </div>
                                    </div>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </BreezeAuthenticatedLayout>
</template>

<script>
import BreezeAuthenticatedLayout            from '@/Layouts/Authenticated.vue'
import {Head}                               from '@inertiajs/inertia-vue3';
import {reactive, onMounted, toRefs, watch} from 'vue';

export default {
    components : {
        BreezeAuthenticatedLayout,
        Head,
    },
    setup() {
        let data = reactive({
            posts  : [],
            search : '',
        });
        let getPosts = () => {
            axios.get('/posts', {
                params : {
                    search : data.search,
                },
            }).then(response => {
                data.posts = response.data.data;
            }).catch(error => {
                console.log(error);
            });
        };

        onMounted(getPosts);

        watch(
            () => data.search,
            _.debounce((newVal, oldVal) => {
                if (newVal !== oldVal) {
                    getPosts();
                }
            }, 500),
        );

        let formSubmitHandler = _.debounce(() => {
            getPosts();
        }, 500);

        return {
            ...toRefs(data),
            formSubmitHandler,
        }
    },
}
</script>
