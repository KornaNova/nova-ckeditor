<template>
    <div class="footer px-3 bg-white dark:bg-gray-800">
        <Button
            @click.stop="$emit('pick')"
            type="button"
            class="select-btn flex align-middle gap-2"
            size="small"
            icon="check"
            :class="{'opacity-50': !snippet}"
            :disabled="!snippet"
        >
            <span>{{ __('Choose') }}</span>
        </Button>
    </div>
</template>

<script setup>
import {Button} from 'laravel-nova-ui'

defineEmits([
    'pick'
])


const props = defineProps({
    snippet: {
        type: Object,
        default: () => ({})
    }
})
</script>

<style lang="scss" scoped>
.footer {
    position: relative;
    display: flex;
    align-items: center;
    justify-content: flex-end;
    height: 50px;
    box-shadow: 0 0 6px rgba(46, 49, 56, 0.05);
    z-index: 100;
}

[dir=rtl] {
    .select-btn {
        right: auto;
        left: 8px;
        flex-direction: row-reverse;
    }
}
</style>
