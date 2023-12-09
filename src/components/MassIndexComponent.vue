<template>
    <div class="mass-index-container">
        <label>Индекс массы тела</label>
        <div>
            <label for="height">Рост, см</label>
            <input type="number" name="" id="height" v-model="height">
            <label for="weight">Вес, кг</label>
            <input type="number" name="" id="weight" v-model="weight">
        </div>
        <div>{{ resultString }}</div>
    </div>
</template>

<script>
export default {
    data() {
        return {
            height: 0,
            weight: 0
        }
    },
    computed: {
        massIndex() {
            return this.weight / (this.height / 100) ** 2;
        },
        interpretation() {
            if (this.massIndex < 25) return 'Норма'
            else if (this.massIndex >= 25 && this.massIndex < 30) return 'Избыточный вес';
            else if (this.massIndex >= 30 && this.massIndex < 35) return 'Ожирение I';
            else if (this.massIndex >= 35 && this.massIndex < 40) return 'Ожирение II';
            else if (this.massIndex >= 40) return 'Ожирение III';
        },
        resultString() {
            return (this.height > 0 && this.weight > 0) ? `ИМТ = ${this.massIndex.toFixed(2)} кг/м^2 (${this.interpretation})` : '';
        }
    }
}
</script>

<style  scoped>
.mass-index-container {
    padding: 10px;
    margin-top: 15px;
    /* display: flex;
    flex-direction: column; */
    width: 85%;
    border: rgb(65, 65, 65) 1px solid;
    border-radius: 8px;
}

.mass-index-container input {
    width: 48px;
    margin-left: 16px;
    margin-right: 16px;
}
</style>