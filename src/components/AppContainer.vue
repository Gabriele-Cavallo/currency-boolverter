<script>
import axios from 'axios';
import InputAmount from './InputAmount.vue';
import ConvertedValue from './ConvertedValue.vue';

export default {
    name: 'AppContainer',
    components: {
        InputAmount,
        ConvertedValue
    },
    data(){
        return {
            currencies: {},
            convertedValue: undefined,
            amount: undefined,
            from: 'EUR',
            to: '',
        }
    },
    methods: {
        async getAllCurrencies() {
            await axios.get('https://api.frankfurter.app/currencies')
            .then(response => this.currencies = response.data);
        },

        convertValue(from, to, amount) {
        fetch(`https://api.frankfurter.app/latest?base=${from}&symbols=${to}`)
            .then((resp) => resp.json())
            .then((data) => {
            const convertedAmount = (amount * data.rates[to]).toFixed(2);
            this.convertedValue = +convertedAmount;
            });
        }
    },
    mounted() {
        this.getAllCurrencies();
    },
    watch: {
        amount: {
            handler(newValue) {
                if (newValue && newValue !== undefined && this.to) {
                    this.convertValue(this.from, this.to, this.amount);
                }
            }
        },
        from: {
            handler(newValue) {
                if (newValue && this.to) {
                    this.convertValue(this.from, this.to, this.amount);
                }
            }
        },
        to: {
            handler(newValue) {
                if (newValue && newValue !== '') {
                    this.convertValue(this.from, this.to, this.amount);
                }
            }
        },
    },
}
</script>

<template>
    <section id="main-container" class="grid place-items-center bg-gray-700">
        <div class="converter-container border-2 border-green-500 rounded-lg py-3 px-2">
            <h1 class="text-green-500 text-center text-3xl mb-1">CURRENCY BOOLVERTE</h1>
            <div v-if="amount &&  from" class="text-green-200 text-xs">{{ amount }} {{ from }} è uguale a</div>
            <div v-if="amount &&  from && to"  class="text-green-200 text-3xl">{{ convertedValue }} {{ to }}</div>
            <InputAmount v-model:amount="amount" v-model:from="from" :currencies = currencies />
            <ConvertedValue v-model:to="to" :convertedValue = convertedValue :currencies = currencies />
        </div>
    </section>
</template>

<style scoped lang="scss">
section#main-container{
    width: 100vw;
    height: 100vh;
    .converter-container {
        width: 50%;
        height: 80%;
    }
}
</style>