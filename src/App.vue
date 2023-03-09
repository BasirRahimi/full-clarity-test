<template>
    <div class="d-flex justify-content-center align-items-center h-100">
        <BaseButton class="btn btn-primary" @click="modal.show()">Create a new group</BaseButton>
        <NewGroupModal ref="modal"/>
    </div>
</template>

<script setup>
import { ref } from 'vue';
import BaseButton from '@/components/base-components/BaseButton.vue';
import NewGroupModal from '@/components/NewGroupModal.vue';

const modal = ref();
</script>

<style scoped lang="scss">
</style>