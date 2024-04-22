<template>
    <div class="analys-card">
        <div class="list-controls">
            <h2>{{ analysTitle }}</h2>
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
                id: '',
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
            let _resultObject = {};
            if (labItem.hasOwnProperty('isLiteral') && labItem.isLiteral == true) {
                if (itemValue != '') {
                    let _itemID = _ResultArray.findIndex(x => x.item.itemType === labItem.itemType);
                    if (_itemID >= 0) {
                        _ResultArray[_itemID].value = itemValue;
                    }
                    else {
                        let FinalObject = {};
                        FinalObject.item = labItem;
                        FinalObject.value = itemValue;
                        _ResultArray.push(FinalObject);
                    }
                }
                else {
                    let _itemID = _ResultArray.findIndex(x => x.item.itemType === labItem.itemType);
                    if (_itemID >= 0) {
                        _ResultArray.splice(_itemID, 1);
                        //console.log(`Removed ${labItem.itemType}`);
                    }
                }
            }
            else {
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
            }

            _ResultArray.sort((a, b) => this.srcArray.indexOf(a.item) - this.srcArray.indexOf(b.item));
            // this.resultArray = _ResultArray;
            _resultObject = { title: this.analysTitle, id: this.modelValue.id, results: _ResultArray,  date: this.dateToString(new Date(this.date)) };

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

<style scoped lang="sass">
@import '../assets/styles/style.sass'
.analys-card 
    background-color: rgba(255,255,255,0.3) 
    @include border
    margin: 25px
    width: auto
    box-shadow: 0 6px 14px black 
    & .list-controls 
        display: flex
        flex-direction: row
        justify-content: space-between
        align-items: center
        font-size: 24px
        & h2
            background-color: rgba(255,255,255,0.3)
            padding-left: 15px
            padding-right: 15px
            padding-top: 10px
        & select
            margin-right: 10px
        & input[type="date"], & select 
            font-size: 15px
            border: none
    & .table 
        width: 100%
        // margin-bottom: 20px
        border-collapse: collapse
        & th 
            font-weight: 400
            padding: 10px 0
            background: rgba(255,255,255,0.3)
        & tr td:first-child,
        & tr th:first-child 
            border-left: none
        & tr td:last-child,
        & tr th:last-child 
            border-right: none
            border-bottom: none

</style>