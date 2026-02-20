<script setup>
import { ref } from 'vue';
import { useForm, router } from '@inertiajs/vue3';

const props = defineProps({
    tasks: Array
});

const form = useForm({
    title: '',
    description: ''
});

const submit = () => {
    form.post('/tasks', {
        onSuccess: () => form.reset(),
    });
};

const toggleComplete = (task) => {
    router.patch(`/tasks/${task.id}`);
};

const deleteTask = (task) => {
    if (confirm('Bạn có chắc chắn muốn xóa công việc này không?')) {
        router.delete(`/tasks/${task.id}`);
    }
};
</script>

<template>
    <div class="min-h-screen bg-gray-50 py-12 px-4 sm:px-6 lg:px-8">
        <div class="max-w-3xl mx-auto">
            <div class="bg-white rounded-2xl shadow-xl overflow-hidden">
                <!-- Header -->
                <div class="bg-gradient-to-r from-blue-600 to-indigo-600 px-8 py-10 text-white">
                    <h1 class="text-3xl font-extrabold tracking-tight">Home - Task Manager</h1>
                    <p class="mt-2 text-blue-100 italic">Quản lý công việc của bạn hiệu quả hơn.</p>
                </div>

                <!-- Create Form -->
                <div class="p-8 border-b border-gray-100 bg-blue-50/30">
                    <form @submit.prevent="form.post('/tasks', { onSuccess: () => form.reset() })" class="space-y-4">
                        <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
                            <div>
                                <label class="block text-sm font-semibold text-gray-700 mb-1">Tiêu đề</label>
                                <input v-model="form.title" type="text" placeholder="Nhập tên công việc..."
                                    class="w-full px-4 py-3 rounded-xl border border-gray-200 focus:ring-2 focus:ring-blue-500 focus:border-transparent transition-all outline-none bg-white">
                                <div v-if="form.errors.title" class="text-red-500 text-xs mt-1">{{ form.errors.title }}
                                </div>
                            </div>
                            <div>
                                <label class="block text-sm font-semibold text-gray-700 mb-1">Mô tả (tùy chọn)</label>
                                <input v-model="form.description" type="text" placeholder="Thêm ghi chú..."
                                    class="w-full px-4 py-3 rounded-xl border border-gray-200 focus:ring-2 focus:ring-blue-500 focus:border-transparent transition-all outline-none bg-white">
                            </div>
                        </div>
                        <div class="flex justify-end">
                            <button type="submit" :disabled="form.processing"
                                class="bg-blue-600 hover:bg-blue-700 text-white font-bold py-3 px-8 rounded-xl transition-all flex items-center gap-2 transform active:scale-95 disabled:opacity-50 shadow-lg shadow-blue-200">
                                <span v-if="form.processing">Đang lưu...</span>
                                <span v-else>Thêm công việc</span>
                            </button>
                        </div>
                    </form>
                </div>

                <!-- List Section -->
                <div class="p-8">
                    <h2 class="text-xl font-bold text-gray-800 mb-6 flex items-center gap-2">
                        Danh sách công việc
                        <span class="bg-blue-100 text-blue-600 text-xs py-1 px-2.5 rounded-full font-bold">{{
                            tasks.length }}</span>
                    </h2>

                    <div v-if="tasks.length === 0"
                        class="text-center py-20 bg-gray-50 rounded-2xl border-2 border-dashed border-gray-200">
                        <div class="text-gray-400 mb-2">
                            <svg class="w-12 h-12 mx-auto mb-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                                    d="M9 5H7a2 2 0 00-2 2v12a2 2 0 002 2h10a2 2 0 002-2V7a2 2 0 00-2-2h-2M9 5a2 2 0 002 2h2a2 2 0 002-2M9 5a2 2 0 012-2h2a2 2 0 012 2">
                                </path>
                            </svg>
                        </div>
                        <p class="text-gray-500 font-medium">Chưa có công việc nào. Hãy bắt đầu thôi!</p>
                    </div>

                    <ul v-else class="space-y-4">
                        <li v-for="task in tasks" :key="task.id"
                            class="group flex items-center justify-between p-5 bg-white border border-gray-100 rounded-2xl hover:shadow-md transition-all border-l-4"
                            :class="task.is_completed ? 'border-l-emerald-400 bg-emerald-50/20' : 'border-l-blue-400'">

                            <div class="flex items-center gap-4 flex-1">
                                <button @click="toggleComplete(task)"
                                    class="w-6 h-6 rounded-full border-2 flex items-center justify-center transition-colors shadow-inner"
                                    :class="task.is_completed ? 'bg-emerald-500 border-emerald-500' : 'border-gray-200 hover:border-blue-400'">
                                    <svg v-if="task.is_completed" class="w-4 h-4 text-white" fill="none"
                                        stroke="currentColor" viewBox="0 0 24 24">
                                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="3"
                                            d="M5 13l4 4L19 7"></path>
                                    </svg>
                                </button>

                                <div class="flex-1">
                                    <h3 class="font-bold text-gray-800 transition-all"
                                        :class="{ 'line-through text-gray-400': task.is_completed }">
                                        {{ task.title }}
                                    </h3>
                                    <p v-if="task.description" class="text-sm text-gray-500 mt-0.5">{{ task.description
                                    }}</p>
                                </div>
                            </div>

                            <div class="flex items-center gap-2 opacity-0 group-hover:opacity-100 transition-opacity">
                                <button @click="deleteTask(task)"
                                    class="p-2 text-gray-400 hover:text-red-500 hover:bg-red-50 rounded-lg transition-all">
                                    <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                                            d="M19 7l-.867 12.142A2 2 0 0116.138 21H7.862a2 2 0 01-1.995-1.858L5 7m5 4v6m4-6v6m1-10V4a1 1 0 00-1-1h-4a1 1 0 00-1 1v3M4 7h16">
                                        </path>
                                    </svg>
                                </button>
                            </div>
                        </li>
                    </ul>
                </div>
            </div>

            <!-- Footer Meta -->
            <p class="mt-8 text-center text-gray-400 text-sm">
                Xây dựng bởi Antigravity • Laravel + Inertia + Vue + Tailwind
            </p>
        </div>
    </div>
</template>

<style>
body {
    font-family: 'Instrument Sans', sans-serif;
}
</style>
