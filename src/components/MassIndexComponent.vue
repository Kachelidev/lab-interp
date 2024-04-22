<template>
    <div class="mass-index-container">
        <div class="title">
            <label>Индекс массы тела</label>
        </div>
        <div class="properties">
            <CustomInput title="Рост" v-model="height" />
            <CustomInput title="Вес" v-model="weight" />
        </div>
        <div class="result-container">
            <pre class="result">{{ resultString }}</pre>
            <CopyButon @onClicked="CopyMassIndex" />
        </div>
    </div>
</template>

<script>
import CopyButon from './CopyButon.vue';
import CustomInput from './CustomInput.vue';

export default {
    data() {
        return {
            height: 0,
            weight: 0
        };
    },
    methods: {
        CopyMassIndex() {
            if (this.height > 0 && this.weight > 0) {
                const result = `Рост - ${this.height} см, Вес - ${this.weight} кг\n${this.resultString}`;
                navigator.clipboard.writeText(result);
            }
        }
    },
    computed: {
        massIndex() {
            return this.weight / (this.height / 100) ** 2;
        },
        interpretation() {
            if (this.massIndex < 25)
                return 'Норма';
            else if (this.massIndex >= 25 && this.massIndex < 30)
                return 'Избыточный вес';
            else if (this.massIndex >= 30 && this.massIndex < 35)
                return 'Ожирение I';
            else if (this.massIndex >= 35 && this.massIndex < 40)
                return 'Ожирение II';
            else if (this.massIndex >= 40)
                return 'Ожирение III';
        },
        resultString() {
            return (this.height > 0 && this.weight > 0) ? `ИМТ = ${this.massIndex.toFixed(2)} кг/м^2\n(${this.interpretation})` : 'Данные не верны';
        }
    },
    components: { CopyButon, CustomInput }
}
</script>

<style  scoped lang="sass">
@import '../assets/styles/style.sass'
.mass-index-container 
    @include border 
    min-width: 220px
    display: flex
    flex-direction: column
    justify-content: space-between
    align-items: stretch
    background: #fff
    input
        width: 48px
        margin-left: 16px
        margin-right: 16px
    .title 
        background: #25b7c4
        text-align: center
        font-size: 24px
    .properties 
        display: flex
        flex-direction: row
        gap: 5px
        justify-content: flex-start
        align-items: center
        .property-item 
            display: flex
            flex-direction: column
            gap: 5px
            align-items: center
            justify-content: center
    .result-container
        display: flex
        flex-direction: row
        justify-content: space-between
        align-items: center
        gap: 5px
        padding: 0px 5px
        margin-bottom: 5px
        .result
            font-size: 16px
</style>