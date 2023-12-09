<template>
    <div class="filtration-speed-container">
        <label>Скорость клубочковой фильтрации</label>
        <label>Креатинин - {{ creatininValue }}</label>
        <div class="controls-radio">
            <label for="">Ед. измерения:</label>
            <input type="radio" name="" id="one" value="mkmol" v-model="units">
            <label for="one">мкмоль/л</label>
            <input type="radio" name="" id="three" value="mg" v-model="units">
            <label for="three">мг/дл</label>
        </div>
        <div class="controls-inputs">
            <!-- <label for="height">Рост, см</label>
            <input type="number" name="" id="height">
            <label for="weight">Вес, кг</label>
            <input type="number" name="" id="weight"> -->
            <label for="age">Возраст, лет</label>
            <input type="number" name="" id="age" v-model="age">
        </div>
        <label class="result">{{ resultString }} </label>
    </div>
</template>

<script>
export default {
    data() {
        return {
            units: 'mkmol',
            age: 0
        }
    },
    props: {
        creatininValue: {
            type: Number,
            default: -1
        },
        gender: 'muscle'
    },
    computed: {
        creatininFinal() {
            switch (this.units) {
                case 'mkmol': return Number(this.creatininValue) * 0.0113
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
            if (this.age >= 18) return `СКФ = ${this.result} мл/мин/1.73м^2 (ХПН ${this.interpretation})`;
            else if (this.age < 18 && this.age > 0) return 'СКФ расчитывается для людей старше 18 лет';
            else if (this.age <= 0) return 'Возраст не верен';
        },
        interpretation() {
            let _interpResult = '0';
            if (this.result >= 90) _interpResult = '1';
            else if (this.result >= 60 && this.result < 90) _interpResult = '2'
            else if (this.result >= 45 && this.result < 60) _interpResult = '3а'
            else if (this.result >= 30 && this.result < 45) _interpResult = '3б'
            else if (this.result >= 15 && this.result < 30) _interpResult = '4'
            else if (this.result >= 0 && this.result < 15) _interpResult = '5'
            return _interpResult
        }
    }
}
</script>

<style scoped>
.filtration-speed-container {
    padding: 10px;
    margin-top: 15px;
    display: flex;
    flex-direction: column;
    width: 85%;
    border: rgb(65, 65, 65) 1px solid;
    border-radius: 8px;
}

.controls-inputs {
    border-bottom: rgb(65, 65, 65) 1px solid;
    padding-bottom: 5px;
}

.controls-inputs input {
    width: 48px;
    margin-left: 16px;
    margin-right: 16px;
}

.result {
    padding-top: 10px
}
</style>