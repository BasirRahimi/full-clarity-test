<template>
    <BaseModal :show-close="false" ref="modal" static>
        Testing Modal
    </BaseModal>
</template>

<script setup>
import { ref } from 'vue';
import BaseModal from '@/components/base-components/BaseModal.vue';

const modal = ref();

const show = () => {
    modal.value.show();
}

defineExpose({ show });

</script>