<script>
import axios from 'axios';
import InputAmount from './InputAmount.vue';
import ApexCharts from 'apexcharts';

export default {
    name: 'AppContainer',
    components: {
        InputAmount,
    },
    data(){
        return {
            currencies: {},
            convertedValue: undefined,
            amount: undefined,
            from: '',
            to: '',
            chartData: {}
        }
    },
    methods: {
        async getAllCurrencies() {
            await axios.get('https://api.frankfurter.app/currencies')
            .then(response => this.currencies = response.data);
        },

        convertValue(from, to, amount) {
            const today = new Date();
            const sevenDayAgo = new Date(today -168 * 3600* 1000);

            let day = sevenDayAgo.getDate();
            if(day.toString().length < 2){
                day = '0' + day;
            }

            const month = sevenDayAgo.getMonth() + 1;
            const year = sevenDayAgo.getFullYear();

            fetch(`https://api.frankfurter.app/latest?base=${from}&symbols=${to}`)
                .then((resp) => resp.json())
                .then((data) => {
                    const convertedAmount = (amount * data.rates[to]).toFixed(2);
                    this.convertedValue = +convertedAmount;
                })
                .then(
                    fetch(`https://api.frankfurter.app/${year}-${month}-${day}..?from=${from}&to=${to}`)
                    .then((response) => response.json())
                    .then((data) => this.chartData = data.rates)
                )
                
        },
        createChart(){
            const categories = Object.keys(this.chartData);
            const seriesData = categories.map(date => this.chartData[date][this.to]);

            const options = {
                chart: {
                    type: 'line',
                    height: '90%',
                },
                series: [{
                    name: 'Exchange rate',
                    data: seriesData,
                }],
                xaxis: {
                    categories: categories,
                    labels: {
                    style: {
                            colors: '#bbf7d0', // Colore delle etichette dell'asse X
                            fontSize: '12px'
                        }
                    },
                    title: {
                        text: 'Date',
                        style: {
                            color: '#bbf7d0', // Colore del titolo dell'asse X
                            fontSize: '14px'
                        }
                    }
                },
                yaxis: {
                    labels: {
                        style: {
                            colors: '#bbf7d0', // Colore delle etichette dell'asse Y
                            fontSize: '12px'
                        }
                    },
                    title: {
                        text: 'Exchange Rate',
                        style: {
                            color: '#bbf7d0', // Colore del titolo dell'asse Y
                            fontSize: '14px'
                        }
                    }
                },
                title: {
                    text: `Cambio ${this.from} -> ${this.to}`,
                    align: 'center',
                    style: {
                        color: '#bbf7d0', // Colore del titolo dell'asse Y
                        fontSize: '14px'
                    }
                },
                colors: ['#22c55e'],
                stroke: {
                    width: 2, // Spessore della linea
                    curve: 'smooth' // Curvatura della linea
                },
                markers: {
                    size: 3, // Dimensione dei punti sui dati
                    colors: ['#bbf7d0'], // Colore dei punti
                    strokeColors: '#bbf7d0', // Colore del bordo dei punti
                    strokeWidth: 2
                },
                grid: {
                    borderColor: '#bbf7d0' // Colore della griglia
                }
            }
            const chart = new ApexCharts(document.querySelector('#chart'), options);
            chart.render();
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
                if(this.from === '' || this.to === '') {
                    this.convertedValue = undefined;
                }
            }
        },
        to: {
            handler(newValue) {
                if (newValue && newValue !== '') {
                    this.convertValue(this.from, this.to, this.amount);
                }
                if(this.from === '' || this.to === '') {
                    this.convertedValue = undefined;
                }
            }
        },
        chartData: {
            handler(newValue) {
                if(newValue){
                    this.createChart();
                }
            }
        },
    },
}
</script>

<template>
    <section id="main-container" class="grid place-items-center bg-gray-700">
        <div class="converter-container flex flex-col border-2 border-green-500 rounded-lg py-3 px-2">
            <div>
                <h1 class="text-green-500 text-center text-3xl mb-1">CURRENCY BOOLVERTE</h1>
                <div v-if="amount &&  from" class="text-green-200 text-xs">{{ amount }} {{ from }} è uguale a</div>
                <div v-if="amount &&  from && to"  class="text-green-200 text-3xl">{{ convertedValue }} {{ to }}</div>
                <InputAmount v-model:amount="amount" v-model:from="from" :currencies = currencies v-model:to="to" :convertedValue = convertedValue />
            </div>
            <div class="chart-container">
                <div v-if="amount && amount !== 0 && from !== '' && to !== ''" id="chart">
                </div>
            </div>
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
        .chart-container{
            height: 100%;
            #chart {
                max-width: 90%;
                margin: 35px auto;
                height: 100%;
            }
        }
    }
}
</style>