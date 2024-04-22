<template>
    <div class="result-item-container">
        <pre>{{ this.GetResultText() }}</pre>
        <CopyButon @on-clicked="CopyResultText" />
    </div>
</template>

<script>
import CopyButon from './CopyButon.vue';

export default {
    components: { CopyButon },
    data() {
        return {
            finalString: {
                type: String,
                default: ''
            }
        };
    },
    props: {
        data: {
            type: Object,
            default: {}
        },
        ID: {
            type: Number,
            default: -1
        },
        text_mode: {
            type: String,
            default: "full_col"
        },
        use_ref: false,
        use_abbrev: true,
        use_units: true,
        modelValue: '',
        mode:'muscle'
    },
    methods: {
        GetResultText() {
            let _separator = '';
            let _resultStrings = [];
            let _thereIsNoResults = true;
            switch (this.text_mode) {
                case 'full_col':
                    _separator = '\n';
                    break;
                case 'full_line':
                    _separator = ', ';
                    break;
            }
            const _analys = this.data;
            let _labItemFinalstrings = [];
            if (_analys.results.length > 0) {
                const _titleNdate = `${(_thereIsNoResults) ? '' : '\n'}${_analys.title} от ${_analys.date}:\n`;
                _thereIsNoResults = false;
                for (let j = 0; j < _analys.results.length; j++) {
                    const labItem = _analys.results[j];
                    const _analysTitle = this.use_abbrev ? labItem.item.abrreviature : labItem.item.title;
                    const _analysUnits = (!labItem.item.hasOwnProperty('isLiteral') && this.use_units && !labItem.item.isLiteral) ? labItem.item.units : '';
                    const _analysRefs = (!labItem.item.hasOwnProperty('isLiteral') && this.use_ref && !labItem.item.isLiteral) ? this.GetReference(labItem.item) : '';
                    let _finalString = `${_analysTitle}: ${labItem.value} ${_analysUnits} ${_analysRefs}`;
                    _labItemFinalstrings.push(_finalString.trim());
                }
                _resultStrings.push(_titleNdate + _labItemFinalstrings.join(_separator));
            }
            return _resultStrings.join('\n');
        },
        CopyResultText() {
            navigator.clipboard.writeText(this.analys_result_string);
        },
        GetReference(item) {
            if (this.mode === 'muscle') {
                return `(${item.references.MusMin}-${item.references.MusMax})`;
            }
            else if (this.mode === 'femini') {
                return `(${item.references.FemMin}-${item.references.FemMax})`;
            }
        },
    },
    emits: [
        'update:modelValue'
    ],
    computed: {
        analys_result_string() {
            return this.GetResultText();
        }
    },
    watch: {
        analys_result_string(newResult) {
            this.$emit('update:modelValue', newResult);
        }
    }

}
</script>

<style scoped lang="sass">
@import '../assets/styles/style.sass'
.result-item-container 
    display: flex
    flex-direction: row
    justify-content: space-between
    align-items: center
    height: auto
    background-color: #fff
    @include border
    border-left: 6px solid $gradient-first 
    padding: 5px 10px
    & pre
        margin-right: 15px
</style>