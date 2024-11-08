<script>
export default {
    name: 'InputAmount',
    data() {
        return {
            selectedCurrency: 'EUR',
            amountDefault: undefined,
        }
    },
    props: {
        currencies: Object,
        amount: Number,
        from: String,
    },
}   
</script>

<template>
    <section class="my-2">
        <div class="flex overflow-hidden border-2 rounded-md border-green-500">
            <div class="border-e-2 border-green-500 w-[50%]">
                <input @keyup="$emit('update:amount', +amountDefault)" v-model="amountDefault" class="outline-none bg-green-200 w-full text-center" type="number" name="user-input" id="user-input">
            </div>
            <div class="w-[50%]">
                <select class="w-full bg-green-200 text-center" @change="$emit('update:from', selectedCurrency)" v-model="selectedCurrency" name="" id="">
                    <option :value="'EUR'">{{ currencies.EUR }}</option>
                    <option v-for="currencie, key in currencies" :key="key" :value="key" >{{ currencie }}</option>
                </select>
            </div>
        </div>
    </section>
</template>

<style lang="scss" scoped></style>
