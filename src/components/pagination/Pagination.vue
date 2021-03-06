<template>
    <div class="pagination" :class="[order, size, { 'is-simple': simple }]">
        <a role="button" href="#" class="pagination-previous" @click.prevent="prev"  :disabled="!hasPrev">
            <b-icon icon="chevron-left" both></b-icon>
        </a>
        <a role="button" href="#" class="pagination-next" @click.prevent="next" :disabled="!hasNext">
            <b-icon icon="chevron-right" both></b-icon>
        </a>
        <ul class="pagination-list" v-if="!simple">
            <!--First-->
            <li v-if="hasFirst"><a role="button" href="#" class="pagination-link" @click.prevent="first">1</a></li>
            <li v-if="hasFirstEllipsis"><span class="pagination-ellipsis">&hellip;</span></li>

            <!--Pages-->
            <li v-for="page in pagesInRange" :key="page.number">
                <a role="button" href="#" class="pagination-link" @click.prevent="page.click" :class="{ 'is-current': page.isCurrent }">
                    {{ page.number }}
                </a>
            </li>

            <!--Last-->
            <li v-if="hasLastEllipsis"><span class="pagination-ellipsis">&hellip;</span></li>
            <li v-if="hasLast"><a role="button" href="#" class="pagination-link" @click.prevent="last">{{ pageCount }}</a></li>
        </ul>
        <small class="info" v-if="simple">
            {{ firstItem }}-{{ current * perPage }} / {{ total }}
        </small>
    </div>
</template>

<script>
    import Icon from '../icon'

    export default {
        name: 'bPagination',
        components: {
            [Icon.name]: Icon
        },
        props: {
            total: [Number, String],
            perPage: {
                type: [Number, String],
                default: 20
            },
            current: {
                type: [Number, String],
                default: 1
            },
            size: String,
            simple: Boolean,
            order: String
        },
        computed: {
            /**
             * Total page size (count).
             */
            pageCount() {
                return Math.ceil(this.total / this.perPage)
            },

            /**
             * First item of the page (count).
             */
            firstItem() {
                const firstItem = this.current * this.perPage - this.perPage + 1
                return firstItem >= 0 ? firstItem : 0
            },

            /**
             * Check if previous button is available.
             */
            hasPrev() {
                return this.current > 1
            },

            /**
             * Check if first page button should be visible.
             */
            hasFirst() {
                return this.current >= 3
            },

            /**
             * Check if first ellipsis should be visible.
             */
            hasFirstEllipsis() {
                return this.current >= 4
            },

            /**
             * Check if last page button should be visible.
             */
            hasLast() {
                return this.current <= this.pageCount - 2
            },

            /**
             * Check if last ellipsis should be visible.
             */
            hasLastEllipsis() {
                return this.current < this.pageCount - 2 && this.current <= this.pageCount - 3
            },

            /**
             * Check if next button is available.
             */
            hasNext() {
                return this.current < this.pageCount
            },

            /**
             * Get near pages, 1 before and 1 after the current.
             * Also add the click event to the array.
             */
            pagesInRange() {
                if (this.simple) return

                const left = Math.max(1, this.current - 1)
                const right = Math.min(this.current + 1, this.pageCount)

                const pages = []
                for (let i = left; i <= right; i++) {
                    pages.push({
                        number: i,
                        isCurrent: this.current === i,
                        click: (event) => {
                            this.$emit('change', i)
                            this.$emit('update:current', i)

                            // Set focus on element to keep tab order
                            this.$nextTick(() => event.target.focus())
                        }
                    })
                }
                return pages
            }
        },
        watch: {
            /**
             * If current page is trying to be greater than page count, set to last.
             */
            pageCount(value) {
                if (this.current > value) this.last()
            }
        },
        methods: {
            /**
             * Previous button click listener.
             */
            prev() {
                if (!this.hasPrev) return
                this.$emit('change', this.current - 1)
                this.$emit('update:current', this.current - 1)
            },

            /**
             * First button click listener.
             */
            first() {
                this.$emit('change', 1)
                this.$emit('update:current', 1)
            },

            /**
             * Last button click listener.
             */
            last() {
                this.$emit('change', this.pageCount)
                this.$emit('update:current', this.pageCount)
            },

            /**
             * Next button click listener.
             */
            next() {
                if (!this.hasNext) return
                this.$emit('change', this.current + 1)
                this.$emit('update:current', this.current + 1)
            }
        }
    }
</script>
