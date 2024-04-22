<template>
    <div class="background" @click="Hide" ref="modal">
        <div class="forms" @click.stop="">
            <div class="controls">
                <MassIndexComponent />
                <div class="middle-canvas">
                    <div class="left-side">
                        <div type="button" class="btn-copy" @click="CopyFinalText">Скопировать всё</div>
                        <div class="additional-controls">
                            <label>Расположение </label>
                            <select class="custom-select custom-select-lg mb-3" v-model="finalTextMode">
                                <option selected value="full_col">В столбик</option>
                                <option value="full_line">В строку</option>
                            </select>
                        </div>
                    </div>
                    <div class="checks">
                        <div>
                            <input type="checkbox" name="" id="includePat" v-model="use_units" />
                            <label for="includePat">С ед. измерения</label>
                        </div>
                        <div>
                            <input type="checkbox" name="" id="includeRef" v-model="use_ref" />
                            <label for="includeRef">С нормами</label>
                        </div>
                        <div>
                            <input type="checkbox" name="" id="useAbbreviature" v-model="useAbbrev" />
                            <label for="useAbbreviature">Сокращенные названия</label>
                        </div>
                    </div>
                </div>
                <ClierenceSpeedComponent :creatininValue="creatinin" :gender="gender" />
            </div>
            <div class="result-container">
                <ResultItem v-for="(result, indx) in results" v-show="result.results.length > 0" :ID="indx" :data="result"
                    :mode="gender" :use_units="use_units" :use_abbrev="useAbbrev" :use_ref="use_ref"
                    :text_mode="finalTextMode" v-model="result_strings[indx]">
                </ResultItem>
            </div>
        </div>
    </div>
</template>

<script>
import ClierenceSpeedComponent from '@/components/ClierenseSpeedComponent.vue';
import MassIndexComponent from '@/components/MassIndexComponent.vue';
import ResultItem from '@/components/ResultItem.vue';
import { computed } from 'vue';

export default {
    components: { ClierenceSpeedComponent, MassIndexComponent, ResultItem },
    data() {
        return {
            finalTextMode: 'full_col',
            use_units: false,
            useAbbrev: true,
            use_ref: false,
            result_strings: []
        }
    },
    props: {
        results: {
            type: Array,
            default: []
        },
        gender: ''
    },
    emits: [
        'onHide'
    ],
    methods: {
        focusIt() {
            this.$refs.modal.focus();
        },
        Hide() {
            this.$emit('onHide');
        },
        GenerateFinalText() {
            const _results = this.result_strings;
            let finAL = []
            for (let index = 0; index < _results.length; index++) {
                const element = _results[index];
                if (typeof element === "undefined") continue;

                finAL.push(element);

            }
            return finAL.join('\n\n');
        },

        CopyFinalText() {
            navigator.clipboard.writeText(this.finalText);
        },
    },
    computed: {
        finalText() {
            return this.GenerateFinalText();
        },
        creatinin() {
            const _result_id = this.results.findIndex(x => x.id === 'bca');
            if (_result_id < 0) return 0;
            const _ID = this.results[_result_id].results.findIndex(x => x.item.itemType === 'CREAT');
            return _ID >= 0 ? this.results[_result_id].results[_ID].value : 0;
        }
    }
}
</script>

<style lang="sass" scoped>
@import '../assets/styles/style.sass'
.background
    position: fixed
    top: 0
    bottom: 0
    right: 0
    left: 0
    background-color: $modal-background
    display: flex
    flex-direction: column
    align-items: center
    justify-content: flex-start
    .forms
        display: flex
        flex-direction: column
        justify-content: center
        align-items: center
        gap: 10px
        & .controls
            display: flex
            flex-direction: row
            justify-content: space-around
            align-items: center
            gap: 10px
            padding: 5px 15px
            & .middle-canvas
                @include border
                padding: 5px 10px
                background-color: #fff
                display: flex
                flex-direction: row
                & .left-side
                    display: flex
                    flex-direction: column
                    justify-content: center
                    gap: 10px
                    & .btn-copy
                        height: 48px
                        box-shadow: inset 0 0 0 1px $gradient-first
                        transition-duration: 0.35s
                        text-align: center
                        vertical-align: middle
                        line-height: 48px
                        font-size: 24px
                        &:hover
                            cursor: pointer
                            box-shadow: 0 0 0 32px inset $gradient-first
        .calculators
            display: flex
            flex-direction: row
            gap: 15px
            justify-content: center
        .result-container
            background-color: transparent
            display: flex
            
            justify-content: space-around
            align-items: center
            flex-wrap: wrap
            gap: 5px
            min-width: 400px
            max-width: 800px
            &.line
                flex-direction: column
            &.column
                flex-direction: row
</style>