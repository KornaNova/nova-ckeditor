<template>
    <button @click.stop="open" type="button" class="media-browser-btn" :disabled="disabled">
        <Icon name="pencil" />
        <span>{{ __('Rename') }}</span>
    </button>

    <modal v-model="modalStatus" class="rename-modal" :content-no-overflow="true">
        <template #header>
            <div class="flex items-center gap-2">
                <Icon name="pencil" type="mini" />

                <input
                    @keydown.enter.stop.prevent="submit"
                    @select.stop.prevent
                    v-model="name"
                    ref="input"
                    class="rename-modal__input flex-grow bg-transparent"
                />
            </div>
        </template>

        <template #header-right>
            <div class="flex items-center gap-2">
                <Button @click.stop="close" type="button" variant="ghost" size="small">
                    {{ __('Cancel') }}
                </Button>

                <Button @click.stop="submit" type="button" size="small">
                    {{ __('Submit') }}
                </Button>
            </div>
        </template>
    </modal>
</template>

<script setup>
import {ref, computed, watch, nextTick} from "vue"
import modal from "../modal"
import {Button, Icon} from 'laravel-nova-ui'
import {useLocalization} from 'laravel-nova'
import {selectedItemsProp, typeProp} from "../../utils/picker-props"


// emits
const emit = defineEmits([
    'update:modelValue'
])


// composables
const {__} = useLocalization()


// variables
const props = defineProps({
    selectedItems: selectedItemsProp,
    type: typeProp,
    modelValue: {
        type: String,
        default: ''
    },
    disabled: {
        type: Boolean,
        default: false
    }
})

const modalStatus = ref(false)
const name = ref(props.selectedItems[0]?.name ?? '')
const input = ref(null)



// computed properties
const item = computed(() => {
    return props.selectedItems.length === 1
        ? props.selectedItems[0]
        : null
})



// watchers
watch(
    () => item.value,
    (value) => {
        name.value = value?.name ?? ''
    }
)


// methods
function submit() {
    if (item.value && name.value) {
        const payload = {
            id: item.value.id,
            name: name.value,
            type: props.type
        }


        Nova.request()
            .put('/nova-vendor/nova-ckeditor/update-name', payload)
            .then(() => {
                Nova.success(__('Name updated successfully'))

                emit('update:modelValue', name.value)
                close()
            })
    }
}

function open() {
    modalStatus.value = true

    nextTick(() => {
        input.value?.focus()
    })
}

function close() {
    modalStatus.value = false
}
</script>

<style lang="scss" scoped>
::v-deep(.rename-modal) {
    max-width: 600px;
    height: 80px !important;
    top: calc(50% - 40px) !important;
    left: calc(50% - 300px) !important;
    overflow: hidden;

    .rename-modal__input {
        outline: none;
        //width: 300px;
        height: 62px;
    }

    .modal__header {
        border-bottom: none;
    }
}
</style>
