<script>
export default {
    name: 'InputAmount',
    data() {
        return {
            selectedCurrency: '',
            selectedCurrencyB: '',
            amountDefault: undefined,
        }
    },
    props: {
        currencies: Object,
        amount: Number,
        from: String,
        to: String,
        convertedValue: Number,
    },
    watch: {
        selectedCurrency: {
            handler(newValue){
                if(newValue === ''){
                    this.amountDefault = undefined;
                    this.$emit('update:amount', this.amountDefault);
                    this.$emit('update:from', '');
                }
            }
        },
        selectedCurrencyB: {
            handler(newValue){
                if(newValue === ''){
                    this.$emit('update:to', '');
                }
            }
        },
    }
}   
</script>

<template>
    <section class="my-2">
        <div class="my-2 flex overflow-hidden border-2 rounded-md border-green-500">
            <div class="border-e-2 border-green-500 w-[50%]">
                <input @keyup="$emit('update:amount', +amountDefault)" v-model="amountDefault" class="outline-none bg-green-200 w-full text-center" type="number" name="user-input" id="user-input">
            </div>
            <div class="w-[50%]">
                <select class="w-full bg-green-200 text-center" @change="$emit('update:from', selectedCurrency)" v-model="selectedCurrency" name="" id="">
                    <option selected value="">Seleziona una valuta</option>
                    <option :disabled="key === selectedCurrencyB" v-for="currencie, key in currencies" :key="key" :value="key" >{{ currencie }}</option>
                </select>
            </div>
        </div>
        <div class="my-2 flex overflow-hidden border-2 rounded-md border-green-500">
            <div class="border-e-2 border-green-500 w-[50%]">
                <input  readonly class="outline-none bg-green-200 w-full text-center" type="number" name="user-input" id="user-input" :value="convertedValue">
            </div>
            <div class="w-[50%]">
                <select class="w-full bg-green-200 text-center" @change="$emit('update:to', selectedCurrencyB)" v-model="selectedCurrencyB" name="" id="">
                    <option selected value="">Seleziona una valuta</option>
                    <option :disabled="key === selectedCurrency" v-for="currencie, key in currencies" :key="key" :value="key" >{{ currencie }}</option>
                </select>
            </div>
        </div>
    </section>
</template>

<style lang="scss" scoped></style>
