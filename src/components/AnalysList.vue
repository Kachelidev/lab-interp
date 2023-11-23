<template>
    <div>
        <h2>{{ analysTitle }}</h2>
        <div class="list-controls">
            <input type="date" v-model="date" />
            <select v-model="mode">
                <option disabled value="">Пол</option>
                <option value="muscle">М</option>
                <option value="femini">Ж</option>
            </select>
        </div>
        <table class="table">
            <thead>
                <tr>
                    <th>Название</th>
                    <th>Сокр.название</th>
                    <th>Значение</th>
                    <th>Ед.изм</th>
                    <th colspan="2">Нормальные значения</th>
                </tr>
            </thead>
            <tbody>
                <LabAnItem v-for="item in srcArray" :labItem="item" :key="item.itemType" :mode="mode"
                    @OnValueChanged="ChangeResult" />
            </tbody>
        </table>
    </div>
</template>

<script>
import LabAnItem from '@/components/LabAnItem.vue';

export default {
    components: { LabAnItem },
    data() {
        return {
            date: new Date().toISOString().split('T')[0],
            mode: 'muscle',
            ResultItem: {
                title: '',
                results: [],
                date: this.date
            }
        }
    },
    props: {
        analysTitle: '',
        srcArray: {
            type: Array,
            required: true,
            default: [],
        },
        modelValue: Object
    },
    methods: {
        dateToString(date) {
            return (
                date.getDate() +
                '.' +
                (date.getMonth() + 1) +
                '.' +
                date.getFullYear()
            )
        },
        ChangeResult(labItem, itemValue) {
            let _ResultArray = this.modelValue.results;

            if (itemValue >= 0) {
                let _itemID = _ResultArray.findIndex(x => x.item.itemType === labItem.itemType);
                if (_itemID < 0) {
                    let FinalObject = {};
                    FinalObject.item = labItem;
                    FinalObject.value = itemValue;
                    _ResultArray.push(FinalObject);
                    //console.log(`Added ${labItem.itemType} with value ${itemValue}`);
                }
                else {
                    _ResultArray[_itemID].value = itemValue;
                    //console.log(`Changed ${this.CBAResult[_itemID].item.itemType} with value ${itemValue}`);
                }
            }
            else {
                let _itemID = _ResultArray.findIndex(x => x.item.itemType === labItem.itemType);
                if (_itemID >= 0) {
                    _ResultArray.splice(_itemID, 1);
                    //console.log(`Removed ${labItem.itemType}`);
                }
            }
            _ResultArray.sort((a, b) => this.srcArray.indexOf(a.item) - this.srcArray.indexOf(b.item));
            // this.resultArray = _ResultArray;
            let _resultObject = {
                title: this.analysTitle, results: _ResultArray, date: this.dateToString(new Date(this.date))
            }
            this.ResultItem = _resultObject;
            this.$emit('update:modelValue', _resultObject);
        }
    },
    emits: [
        'update:modelValue'
    ],
    watch: {
        date(newDate) {
            this.ResultItem.date = this.dateToString(new Date(newDate));
            this.$emit('update:modelValue', this.ResultItem);
        }
    }
}

</script>

<style scoped>
.list-controls {
    margin-bottom: 15px;
}

.list-controls input[type="date"],
.list-controls select {
    font-size: 15px;
}

.list-controls select {
    margin-left: 15px;
}

.table {
    width: 100%;
    margin-bottom: 20px;
    border-collapse: collapse;
}

.table th {
    font-weight: bold;
    padding: 5px;
    background: #efefef;
    border: 1px solid #dddddd;
}



.table tr td:first-child,
.table tr th:first-child {
    border-left: none;
}

.table tr td:last-child,
.table tr th:last-child {
    border-right: none;
}
</style>