<script setup lang="ts">
import { ref, computed } from 'vue';
import { Button } from '@/components/ui/button';
import { Table, TableBody, TableCell, TableHead, TableHeader, TableRow } from '@/components/ui/table';
import AppLayout from '@/layouts/AppLayout.vue';
import type { BreadcrumbItem } from '@/types';
import { Head, Link } from '@inertiajs/vue3';
import { defineProps } from 'vue';

const breadcrumbs: BreadcrumbItem[] = [
    { title: 'Dashboard', href: '/admin/dashboard' },
    { title: 'Items', href: '/admin/items' }
];

const props = defineProps<{ items: Array<{ id: number; name: string; description: string; content_type: string; active: boolean }> }>();
const searchQuery = ref('');
const selectedType = ref('');

const uniqueTypes = computed(() => {
    return [...new Set(props.items.map(item => item.content_type))];
});

const filteredItems = computed(() => {
    return props.items.filter(item => {
        const matchesSearch = searchQuery.value ? 
            item.name.toLowerCase().includes(searchQuery.value.toLowerCase()) ||
            item.description?.toLowerCase().includes(searchQuery.value.toLowerCase()) ||
            item.content_type.toLowerCase().includes(searchQuery.value.toLowerCase())
            : true;
        
        const matchesType = selectedType.value ? item.content_type === selectedType.value : true;
        
        return matchesSearch && matchesType;
    });
});
</script>

<template>
    <Head title="Items" />

    <AppLayout :breadcrumbs="breadcrumbs">
        <div class="py-12">
            <div class="mx-auto max-w-7xl sm:px-6 lg:px-8">
                <div class="flex justify-between items-center">
                    <h3 class="text-2xl">Items</h3>
                    <Button :as="Link" :href="route('admin.items.create')">Create Item</Button>
                </div>
                
                <div class="mt-4 flex space-x-4">
                    <input 
                        v-model="searchQuery" 
                        type="text" 
                        placeholder="Search items..." 
                        class="w-full p-2 border rounded-md" 
                    />
                    <select v-model="selectedType" class="p-2 border rounded-md">
                        <option value="">All Types</option>
                        <option v-for="type in uniqueTypes" :key="type" :value="type">{{ type }}</option>
                    </select>
                </div>

                <div class="mt-8 flex flex-col">
                    <Table>
                        <TableHeader>
                            <TableRow>
                                <TableHead>Name</TableHead>
                                <TableHead>Type</TableHead>
                                <TableHead>Active</TableHead>
                                <TableHead class="text-right">Actions</TableHead>
                            </TableRow>
                        </TableHeader>
                        <TableBody>
                            <TableRow v-for="item in filteredItems" :key="item.id">
                                <TableCell>{{ item.name }}</TableCell>
                                <TableCell>{{ item.content_type }}</TableCell>
                                <TableCell>{{ item.active ? 'Active' : 'Inactive' }}</TableCell>
                                <TableCell class="text-right">
                                    <Button :as="Link" variant="link" :href="route('admin.items.edit', item.id)">Edit</Button>
                                </TableCell>
                            </TableRow>
                        </TableBody>
                    </Table>
                </div>
            </div>
        </div>
    </AppLayout>
</template>
