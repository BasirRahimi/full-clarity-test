<template>
    <Teleport to="body">
        <div
            ref="modalEl"
            class="modal fade"
            tabindex="-1"
            :aria-labelledby="labelBy"
            aria-hidden="true">
            <div
                class="modal-dialog"
                :class="{
                    'modal-dialog-centered': centered
                }">
                <div class="modal-content" :class="modalContentClasses">
                    <div
                        class="modal-header"
                        v-if="$slots.header"
                        :class="headerClasses">
                        <slot name="header"></slot>
                        <button
                            v-if="showClose"
                            type="button"
                            class="close"
                            @click="closeModal"
                            aria-label="Close">
                            <span>×</span>
                        </button>
                    </div>

                    <div class="modal-body" :class="bodyClasses">
                        <slot></slot>
                    </div>

                    <div
                        class="modal-footer"
                        :class="footerClasses"
                        v-if="$slots.footer">
                        <slot name="footer"></slot>
                    </div>
                </div>
            </div>
        </div>
    </Teleport>
</template>
<script setup>
import { ref, onMounted } from 'vue';
import { Modal as BsModal } from 'bootstrap';

const modalEl = ref(null);
let modal = null;

const props = defineProps({
    labelBy: {
        type: String,
        default: '',
        description: 'Modal title for accessability'
    },
    showClose: {
        type: Boolean,
        default: true
    },
    modalContentClasses: {
        type: [Object, String],
        description: 'Modal dialog content css classes'
    },
    headerClasses: {
        type: [Object, String],
        description: 'Modal Header css classes'
    },
    bodyClasses: {
        type: [Object, String],
        description: 'Modal Body css classes'
    },
    footerClasses: {
        type: [Object, String],
        description: 'Modal Footer css classes'
    },
    centered: {
        type: Boolean,
        default: true
    },
    static: {
        type: Boolean,
        default: false
    }
});

const hide = () => {
    modal.hide();
};

const show = () => {
    modal.show();
};

const toggle = () => {
    modal.toggle();
};

onMounted(() => {
    modal = new BsModal(modalEl.value, {
        backdrop: props.static ? 'static' : true
    });
});

defineExpose({ show, hide, toggle });
</script>

<style>
.modal.show {
    background-color: rgba(0, 0, 0, 0.3);
}
</style>
