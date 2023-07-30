<script setup>
    import moment from 'moment';
    import vSelect from "vue-select";
    import "vue-select/dist/vue-select.css";
    import 'moment/dist/locale/it';

    moment.locale('it');
</script>

<template> 
    <div class="date-selector">
        <v-select class="single-selector" v-model="selected_day" placeholder="Day" :options="options_days"></v-select>
        <v-select class="single-selector" v-model="selected_month" placeholder="Month" :options="options_months"></v-select>
        <v-select class="single-selector" v-model="selected_year" placeholder="Year" :options="options_years"></v-select>
    </div>
</template>

<script>
    const first_date = moment("01-01-1900", "DD-MM-YYYY");
    var selected_date;
    export default {
        props: {
            //props passato dal parent, contiene una data per il prefill
            start: {
                type: String,
                default: "1-1-1900"
            }
        },

        data() {
            return {
            selected_day: null,
            selected_month: null,
            selected_year: null,
            result: null,
            options_days: [],
            options_months: [],
            options_years: []
            }

        },

        mounted() {
            
            this.updateDaysOptions(),
            this.options_months = moment.months(),
            this.options_years = Array.from({length: moment().year() - first_date.year() + 1}, (value, index) => first_date.year() + index )
            
        },

        created() {
            this.setDefaultValues();
        },

        watch: {
            selected_day (newDay) {this.handleDayChange(newDay)},
            selected_month (newMonth) {this.handleMonthChange(newMonth)},
            selected_year (newYear) {this.handleYearChange(newYear)}
        },

        methods: {

            // genera oggetto moment iniziale. se viene ricevuto un props invalid, inizializza 01-01-1900
            generateStartDate() {
                let start_date = moment(this.start, "DD-MM-YYYY");
                if(start_date.isValid())
                    return start_date;
                else
                    return first_date;
            },

            //inizializza tutti i valori iniziali
            setDefaultValues(){
                selected_date = this.generateStartDate();
                this.selected_day = selected_date.format("DD");
                this.selected_month = selected_date.format('MMMM');
                this.selected_year = selected_date.format("YYYY");
            },

            // si attiva se cambia il select day
            handleDayChange(day){
                selected_date.set('date', day);
                this.checkDateConsistency();
            },

            // si attiva se cambia il select month
            handleMonthChange(month){
                selected_date.set('month', month);
                this.checkDateConsistency();
            },

            // si attiva se cambia il select year
            handleYearChange(year){
                selected_date.set('year', year);
                this.checkDateConsistency();
                
            },

            // setta a null il select day se il giorno non è coerente con la data selezionata
            // attiva emitsResult per restituire il valore corretto al caso all'oggetto parent
            checkDateConsistency(){
                this.updateDaysOptions();
                if (this.selected_day > this.options_days.length){
                    this.selected_day = null;
                }
                if (this.selected_day != null && this.selected_month != null && this.selected_year != null)
                    this.emitsResult(true);
                else
                    this.emitsResult(false);
            },

            // aggiorna le opzioni dei giorni selezionabili in base alla data selezionata
            updateDaysOptions (){
                selected_date.set({'month': this.selected_month, 'year': this.selected_year});  //risolve un presunto overflow (?) di moment.js
                let n_days = selected_date.daysInMonth();
                let array = [];
                for (let i = 1; i <= n_days; i++)
                    array.push(i);
                this.options_days = array;
            },

            //emette al parent la selezione della select
            emitsResult(flag){
                if (flag)
                    this.result = selected_date.toDate();
                else
                    this.result = null;
                this.$emit('result', this.result);
            }
            
        }

    }

</script>

<style scoped>
    .date-selector {
        display: flex;
        flex-direction: row;
        justify-content:space-evenly;
    }

    .single-selector{
        width: 33%
    }

    
</style>
