<template>
    <div
        id="app"
        class="container mx-auto mt-10 px-10"
    >
        <div class="mx-auto mt-20">
            <h3 class="text-center text-lg font-bold mb-4">
                Vue.js autocomplete
            </h3>

            <div class="w-64 mx-auto">
                {{ city }}
                <Autocomplete
                    v-model="city"
                    :options="cities"
                    label-key="label"
                    value-key="id"
                    placeholder="Search cities"
                    @shouldSearch="searchCities"
                    @select="onSelect"
                />
            </div>
        </div>
    </div>
</template>

<script>
    import Autocomplete from '@/components/Autocomplete';

    export default {
        name: 'App',

        components: {
            Autocomplete,
        },

        data() {
            return {
                city: '',
                cities: [],
            };
        },

        methods: {
            searchCities(query) {
                fetch(`http://statecity.test/api/v1/?query=${query}`).then(response => response.json()).then((r) => {
                    this.cities = r.data;
                });
            },

            onSelect(city) {
                console.log(city);
            },
        },
    };
</script>
