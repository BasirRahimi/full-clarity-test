<template>
    <component
        :is="tag"
        :type="tag === 'button' ? nativeType : ''"
        class="btn">
        <span
            class="btn-inner--icon me-2"
            v-if="$slots.icon || (icon && $slots.default)">
            <slot name="icon">
                <i :class="icon"></i>
            </slot>
        </span>
        <i v-if="!$slots.default" :class="icon"></i>
        <span
            class="btn-inner--text"
            v-if="$slots.icon || (icon && $slots.default)">
            <slot>
                {{ text }}
            </slot>
        </span>
        <slot v-if="!$slots.icon && !icon"></slot>
    </component>
</template>
<script setup>

const props = defineProps({
    tag: {
            type: String,
            default: 'button',
            description: 'Button tag (default -> button)'
        },
        nativeType: {
            type: String,
            default: 'button',
            description: 'Button native type (e.g submit,button etc)'
        },
        icon: {
            type: String,
            default: '',
            description: 'Button icon'
        },
        text: {
            type: String,
            default: '',
            description: 'Button text in case not provided via default slot'
        }
})
</script>
<style lang="scss" scoped>
@import '@/assets/variables.scss';

.btn.btn-primary {
    background-color: $btn-primary-bg;
    color: $primary;
    border: 0;

    &:hover {
        color: $white;
        background-color: $primary;
    }
}
</style>
