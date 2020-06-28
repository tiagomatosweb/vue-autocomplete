<template>
    <div class="relative">
        <div class="relative rounded-md">
            <div class="absolute flex items-center inset-y-0 left-0 pl-3 text-gray-400">
                <div style="width: 14px;">
                    <slot name="icon">
                        <svg
                            data-v-8dab3068=""
                            aria-hidden="true"
                            focusable="false"
                            data-prefix="fal"
                            data-icon="search"
                            role="img"
                            xmlns="http://www.w3.org/2000/svg"
                            viewBox="0 0 512 512"
                        ><path
                            data-v-8dab3068=""
                            fill="currentColor"
                            d="M508.5 481.6l-129-129c-2.3-2.3-5.3-3.5-8.5-3.5h-10.3C395 312 416 262.5 416 208 416 93.1 322.9 0 208 0S0 93.1 0 208s93.1 208 208 208c54.5 0 104-21 141.1-55.2V371c0 3.2 1.3 6.2 3.5 8.5l129 129c4.7 4.7 12.3 4.7 17 0l9.9-9.9c4.7-4.7 4.7-12.3 0-17zM208 384c-97.3 0-176-78.7-176-176S110.7 32 208 32s176 78.7 176 176-78.7 176-176 176z"
                            class=""
                        /></svg>
                    </slot>
                </div>
            </div>

            <input
                type="text"
                :value="keyword"
                @input="onInput($event.target.value)"
                :class="inputClassList"
            >

            <button
                type="button"
                class="absolute right-0 top-0 inset-y-0 right-0 pr-3 flex items-center cursor-pointer text-gray-400 focus:outline-none hover:text-gray-400"
                @click="onClear()"
            >
                <svg
                    class="h-2 w-2"
                    stroke="currentColor"
                    fill="none"
                    viewBox="0 0 8 8"
                >
                    <path
                        stroke-linecap="round"
                        stroke-width="1.5"
                        d="M1 1l6 6m0-6L1 7"
                    />
                </svg>
            </button>
        </div>

        <div
            v-show="mutableOptions.length"
            class="absolute right-0 mt-2 w-full rounded-md shadow-lg z-50 overflow-y-scroll"
            style="max-height: 200px;"
        >
            <ul class="py-1 rounded-md bg-white shadow-xs">
                <li
                    v-for="opt in mutableOptions"
                    :key="opt[valueKey]"
                    class="autocomplete-item block px-4 py-2 text-sm leading-5 text-gray-700 cursor-pointer"
                    @click="onSelect(opt)"
                >
                    {{ opt[labelKey] }}
                </li>
            </ul>
        </div>
    </div>
</template>

<script>
    export default {
        name: 'Autocomplete',

        props: {
            value: {
                type: String,
                default: '',
            },

            options: {
                type: Array,
                default: () => [],
            },

            labelKey: {
                type: String,
                default: 'label',
            },

            valueKey: {
                type: String,
                default: 'id',
            },

            searchMinLength: {
                type: Number,
                default: 3,
            },
        },

        data() {
            return {
                keyword: '',
                originalOptions: [],
                mutableOptions: [],
            };
        },

        computed: {
            inputClassList() {
                return [
                    'appearance-none rounded w-full transition duration-150 ease-in-out',
                    this.getTextSizeClass,
                    this.getTextColorClass,
                    this.getBorderColorClass,
                    this.getPaddingClass,
                ];
            },

            getTextSizeClass() {
                return 'text-sm leading-5';
            },

            getTextColorClass() {
                return 'text-gray-800 placeholder-gray-400';
            },

            getBorderColorClass() {
                return 'focus:outline-none border border-gray-40 focus:border-blue-400';
            },

            getPaddingClass() {
                return 'h-10 pr-6 pl-8';
            },
        },

        watch: {
            value(value) {
                this.keyword = value;
            },

            options() {
                this.cloneOptions();
            },
        },

        created() {
            this.keyword = this.value;

            if (this.options.length) {
                this.cloneOptions();
            }
        },

        methods: {
            onInput(vl) {
                this.keyword = vl;
                this.emitInput();

                if (vl.length >= this.searchMinLength) {
                    if (!this.originalOptions.length) {
                        this.$emit('shouldSearch', vl);
                    } else {
                        this.searchInternally();
                    }
                } else {
                    this.resetOptions();
                }
            },

            searchInternally() {
                const search = this.keyword;
                this.mutableOptions = this.originalOptions.filter(o => o[this.labelKey].toLowerCase().search(search.toLowerCase()) >= 0);
            },

            cloneOptions() {
                this.originalOptions = JSON.parse(JSON.stringify(this.options));
                this.mutableOptions = JSON.parse(JSON.stringify(this.options));
            },

            resetOptions() {
                this.originalOptions = [];
                this.mutableOptions = [];
            },

            onSelect(opt) {
                this.$emit('select', opt);
                this.keyword = opt[this.labelKey];
                this.emitInput();
                this.resetOptions();
            },

            emitInput() {
                this.$emit('input', this.keyword);
            },

            resetKeyword() {
                this.keyword = '';
                this.emitInput();
            },

            onClear() {
                this.$emit('select', null);
                this.resetKeyword();
                this.resetOptions();
            },
        },
    };
</script>
