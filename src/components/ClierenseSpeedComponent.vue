<template>
    <div class="filtration-speed-container">
        <label class="title">Скорость клубочковой фильтрации</label>
        <div class="controls-radio">
            <label>Креатинин - {{ creatininValue }}</label>
            <input type="radio" name="" id="one" value="mkmol" v-model="units">
            <label for="one">мкмоль/л</label>
            <input type="radio" name="" id="three" value="mg" v-model="units">
            <label for="three">мг/дл</label>
        </div>
        <CustomInput title="Возраст" v-model="age" />
        <div class="result-container">
            <pre class="result">{{ resultString }}</pre>
            <CopyButon @onClicked="CopyClierenseSpeed" />
        </div>
    </div>
</template>

<script>
import CopyButon from './CopyButon.vue';
import CustomInput from './CustomInput.vue';

export default {
    data() {
        return {
            units: 'mkmol',
            age: 0
        };
    },
    methods: {
        CopyClierenseSpeed() {
            if (this.age >= 18 && this.creatininValue > 0) {
                navigator.clipboard.writeText(this.resultString);
            }
        }
    },
    props: {
        creatininValue: {
            type: Number,
            default: 0
        },
        gender: 'muscle'
    },
    computed: {
        creatininFinal() {
            switch (this.units) {
                case 'mkmol': return Number(this.creatininValue) * 0.0113;
                case 'mg': return Number(this.creatininValue);
                default:
                    break;
            }
        },
        genderK() {
            return this.gender == 'muscle' ? 0.9 : 0.7;
        },
        genderPow() {
            return this.gender == 'muscle' ? -0.302 : -0.241;
        },
        result() {
            const _arg1 = Math.min(this.creatininFinal / this.genderK, 1.0) ** this.genderPow;
            const _arg2 = Math.max(this.creatininFinal / this.genderK, 1.0) ** -1.209;
            const _arg3 = this.gender == 'muscle' ? 1 : 1.012;
            const _result = 142 * _arg1 * _arg2 * 0.9938 ** this.age * _arg3;
            // return `Результат: ${_result.type != undefined ? _result : 'Не все значения введены'}`;
            return _result.toFixed(2);
        },
        resultString() {
            if (this.age >= 18 && this.creatininValue > 0)
                return `СКФ: ${this.result} мл/мин/1.73м^2\n(ХПН ${this.interpretation})`;
            else if (this.age < 18 && this.age > 0)
                return 'СКФ расчитывается для\nлюдей старше 18 лет';
            else if (this.age <= 0)
                return 'Возраст не верен';
            else if (this.creatininValue <= 0)
                return 'Креатинин должен\nбыть больше нуля';
        },
        interpretation() {
            let _interpResult = '0';
            if (this.result >= 90)
                _interpResult = '1';
            else if (this.result >= 60 && this.result < 90)
                _interpResult = '2';
            else if (this.result >= 45 && this.result < 60)
                _interpResult = '3а';
            else if (this.result >= 30 && this.result < 45)
                _interpResult = '3б';
            else if (this.result >= 15 && this.result < 30)
                _interpResult = '4';
            else if (this.result >= 0 && this.result < 15)
                _interpResult = '5';
            return _interpResult;
        }
    },
    components: { CopyButon, CustomInput }
}
</script>

<style scoped lang="sass">
@import '../assets/styles/style.sass'

.filtration-speed-container 
    @include border 
    min-width: 220px
    display: flex
    flex-direction: column
    justify-content: space-between
    align-items: stretch
    background: #fff
    .title
        background: #25b7c4
        text-align: center
        font-size: 24px
    .controls-radio 
        padding-bottom: 5px
        display: flex
        flex-direction: row
        gap: 5px
        justify-content: space-around
        align-items: center
        & input 
            display: none
            &:checked + Label
                border-bottom: 2px black solid
    .result-container
        display: flex
        flex-direction: row
        justify-content: space-between
        align-items: center
        gap: 5px
        padding: 0px 5px
        margin-bottom: 5px
        .result
            font-size: 16pxx
</style>