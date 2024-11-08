<script>
export default {
    name: 'ConvertedValue',
    data() {
        return {
            currencyRateConvert: 0,
            selectedCurrency: '',
        }
    },
    props: {
        currencies: Object,
        to: String,
        convertedValue: Number,
    },
}   
</script>

<template>
    <section class="my-2">
        <div class="flex overflow-hidden border-2 rounded-md border-green-500">
            <div class="border-e-2 border-green-500 w-[50%]">
                <input  readonly class="outline-none bg-green-200 w-full text-center" type="number" name="user-input" id="user-input" :value="convertedValue">
            </div>
            <div class="w-[50%]">
                <select class="w-full bg-green-200 text-center" @change="$emit('update:to', selectedCurrency)" v-model="selectedCurrency" name="" id="">
                    <option selected disabled value="">Seleziona una valuta</option>
                    <option v-for="currencie, key in currencies" :key="key" :value="key" >{{ currencie }}</option>
                </select>
            </div>
        </div>
    </section>
</template>

<style lang="scss" scoped></style>