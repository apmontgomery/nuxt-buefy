<template>
    <div class="collapse">
        <div class="collapse-trigger" @click="toggle">
            <slot name="trigger"></slot>
        </div>
        <transition :name="animation">
            <div class="collapse-content" v-show="isOpen">
                <slot></slot>
            </div>
        </transition>
    </div>
</template>

<script>
    export default {
        name: 'bCollapse',
        props: {
            open: {
                type: Boolean,
                default: true
            },
            animation: {
                type: String,
                default: 'fade'
            }
        },
        data() {
            return {
                isOpen: this.open
            }
        },
        watch: {
            open(value) {
                this.isOpen = value
            }
        },
        methods: {
            /**
             * Toggle and emit events
             */
            toggle() {
                this.isOpen = !this.isOpen
                this.$emit('update:open', this.isOpen)
                this.$emit(this.isOpen ? 'open' : 'close')
            }
        }
    }
</script>
