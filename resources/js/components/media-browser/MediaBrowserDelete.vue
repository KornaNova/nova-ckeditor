<template>
    <button @click.stop="open" type="button" class="media-browser-btn" :disabled="disabled">
        <Icon name="trash" />
        <span>{{ __('Delete') }}</span>
    </button>

    <modal v-model="modalStatus" class="delete-modal" :content-no-overflow="true">
        <template #header>
            <div class="flex items-center gap-1">
                <Icon name="trash" type="mini" />
                <span>{{ __('Remove assets') }}</span>
            </div>
        </template>


        <div class="p-3 overflow-x-hidden">
            <span v-if="selectedItems.length > 1">
                {{ __('Are you sure you want to delete :count files?', {count: selectedItems.length}) }}
            </span>

            <span v-else>
                {{ __('Are you sure you want to delete ":name" file?', {name: selectedItems[0]?.name}) }}
            </span>
        </div>

        <template #footer>
            <Button @click.stop="submit" type="button"  state="danger" size="small">
                {{ __('Delete') }}
            </Button>

            <Button @click.stop="close" type="button" class="mx-2" size="small">
                {{ __('Cancel') }}
            </Button>
        </template>
    </modal>
</template>

<script setup>
import {ref, computed} from "vue"
import modal from "../modal"
import {useLocalization} from 'laravel-nova'
import {Button, Icon} from 'laravel-nova-ui'
import {selectedItemsProp, typeProp} from "../../utils/picker-props"
import {guessResourceKey} from "../../utils/helpers"


// emits
const emit = defineEmits([
    'deleted'
])


// composables
const {__} = useLocalization()


// variables
const props = defineProps({
    selectedItems: selectedItemsProp,
    type: typeProp
})

const modalStatus = ref(false)



// computed properties
const resourceKey = computed(() => {
    return guessResourceKey(props.type)
})

const disabled = computed(() => {
    return props.selectedItems.length === 0
})



// methods
function open() {
    if (!disabled.value) {
        modalStatus.value = true
    }
}

function close() {
    modalStatus.value = false
}

function submit() {
    if (!disabled.value) {
        const resources = props.selectedItems.map(item => `resources[]=${item.id}`).join('&')

        Nova.request()
            .delete(`/nova-api/${resourceKey.value}?${resources}`)
            .then(() => {
                Nova.success(__('Assets have been deleted successfully.'))

                emit('deleted')
            })
            .catch((e) => {
                Nova.error(
                    e?.response?.data?.message
                        ? e.response.data.message
                        : __('Something went wrong! Please try again.')
                )
            })
            .finally(() => {
                close()
            })
    }
}
</script>

<style lang="scss" scoped>
::v-deep(.delete-modal) {
    max-width: 400px;
    height: 180px !important;
    top: calc(50% - 90px) !important;
    left: calc(50% - 200px) !important;
}

.bg-red-500 {
    &:hover {
        background: rgba(var(--colors-red-400));
    }
}
</style>
