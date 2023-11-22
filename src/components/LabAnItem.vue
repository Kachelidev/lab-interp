<template>
    <tr :class="danger">
        <td>{{ labItem.title }}</td>
        <td>{{ labItem.itemType }}</td>
        <td><input type="number" @blur="ChangeColor" v-model.number="val" placeholder="..." @change="ReplaceCharacters">
        </td>
        <td>{{ labItem.units }}</td>
        <td class="reference">{{ mode === 'femini' ? labItem.references.FemMin : labItem.references.MusMin }}</td>
        <td class="reference">{{ mode === 'femini' ? labItem.references.FemMax : labItem.references.MusMax }}</td>
    </tr>
</template>

<script>
export default {
    data() {
        return {
            val: '',
            danger: ''
        }
    },
    props: {
        labItem: Object,
        mode: String
    },
    methods: {
        ChangeColor() {
            let _value = Number(this.val);
            // console.log(_value);
            if (isNaN(_value) || this.val == '' || _value < 0) {
                this.danger = '';
                this.$emit('OnValueChanged', this.labItem, -1);
            }
            else {
                if (_value >= 0.0) {
                    let _min = 0.0;
                    let _max = 0.0;

                    if (this.mode === 'muscle') {
                        _min = this.labItem.references.MusMin;
                        _max = this.labItem.references.MusMax;
                    }
                    else if (this.mode === 'femini') {
                        _min = this.labItem.references.FemMin;
                        _max = this.labItem.references.FemMax;
                    }

                    if (_value >= _min && _value <= _max) {
                        this.danger = 'table-success';
                    }
                    else {
                        this.danger = 'table-danger';
                    }
                }
                else {
                    this.danger = 'table-warning';
                }
                this.$emit('OnValueChanged', this.labItem, this.val);
            }
        },
        ReplaceCharacters() {
            this.val = this.val.toString().replace(',', '.');
        }
    },
    emits: ['OnValueChanged'],
    watch: {
        mode(newMode) {
            this.ChangeColor();
        }
    }
}
</script>

<style  scoped>
input {
    max-width: 100px;
}

.table td {
    border: 1px solid #dddddd;
    padding: 5px;
}

.table tr td:first-child,
.table tr th:first-child {
    border-left: none;
}

.table tr td:last-child,
.table tr th:last-child {
    border-right: none;
}

.table tr td.reference {
    width: auto;
    text-align: center;
}

.table-success {
    background: #5cad55;
}

.table-warning {
    background: #d4d033;
}

.table-danger {
    background: #ec492f;
}
</style>