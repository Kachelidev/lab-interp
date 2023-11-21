<template>
  <div class="tables">

    <AnalysList v-for="(analys, index) in AnalysList" :analysTitle="analys.title" :srcArray="analys.srcList"
      v-model="Results[index]" />

    <div class="forms">
      <div class="controls">
        <button type="button" class="btn btn-outline-dark" @click="CopyFinalText">Скопировать текст</button>
        <select class="custom-select custom-select-lg mb-3" v-model="finalTextMode">
          <option selected value="full_col">В столбик</option>
          <option value="full_line">В строку</option>
        </select>
        <div class="checks">
          <div>
            <input type="checkbox" name="" id="includePat" v-model="use_units" />
            <label for="includePat">С ед. измерения</label>
          </div>
          <div>
            <input type="checkbox" name="" id="includeRef" v-model="use_reference" />
            <label for="includeRef">С нормами</label>
          </div>
        </div>
      </div>
      <pre class="final">{{ finalText }}</pre>
    </div>
  </div>
</template>

<script>
import { ref } from 'vue';
import AnalysList from './components/AnalysList.vue';

export default {
  components: { AnalysList },
  data() {
    return {
      finalTextMode: 'full_col',
      mode: 'muscle',
      use_units: false,
      use_reference: false,
      AnalysList: [
        {
          title: 'Общий анализ крови', srcList: [
            { itemType: 'WBC', title: "Лейкоциты", smallTitle: "", units: "10^9/л", references: { FemMin: 4, FemMax: 9, MusMin: 4, MusMax: 9 } },
            { itemType: 'HGB', title: "Гемоглобин", smallTitle: "", units: "гр/л", references: { FemMin: 120, FemMax: 140, MusMin: 130, MusMax: 160 } },
            { itemType: 'RBC', title: "Эритроциты", smallTitle: "", units: "10^12/л", references: { FemMin: 3.9, FemMax: 4.7, MusMin: 4.0, MusMax: 5.0 } },
            { itemType: 'HCT', title: "Гематокрит", smallTitle: "", units: "%", references: { FemMin: 0.85, FemMax: 1.15, MusMin: 0.85, MusMax: 1.15 } },
            { itemType: 'MCV', title: "Ср. объем эритроцита", smallTitle: "", units: "фЛ", references: { FemMin: 78, FemMax: 94, MusMin: 78, MusMax: 94 } },
            { itemType: 'MCH', title: "Ср. сод. гемоглобина в эр.", smallTitle: "", units: "пгр", references: { FemMin: 26, FemMax: 32, MusMin: 26, MusMax: 32 } },
            { itemType: 'MCHC', title: "Ср.конц-я гемоглобина в эр.", smallTitle: "", units: "гр/л", references: { FemMin: 32.2, FemMax: 36.4, MusMin: 32.2, MusMax: 36.4 } },
            { itemType: 'RDW-CV', title: "Отклонение р-ра эритроцита", smallTitle: "", units: "%", references: { FemMin: 11.6, FemMax: 14.8, MusMin: 11.6, MusMax: 14.8 } },
            { itemType: 'RDW-SD', title: "Разброс размеров  эр.", smallTitle: "", units: "фЛ", references: { FemMin: 35.3, FemMax: 49.9, MusMin: 35.3, MusMax: 49.9 } },
            { itemType: 'PLT', title: "Тромбоциты", smallTitle: "", units: "10^9/л", references: { FemMin: 180, FemMax: 320, MusMin: 180, MusMax: 320 } },
            { itemType: 'MPV', title: "Ср. объем тромбоцита", smallTitle: "", units: "фЛ", references: { FemMin: 7.4, FemMax: 10.4, MusMin: 7.4, MusMax: 10.4 } },
            { itemType: 'PDW', title: "Ширина распр-я тромбоцитов", smallTitle: "", units: "", references: { FemMin: 9.4, FemMax: 18.1, MusMin: 9.4, MusMax: 18.1 } },
            { itemType: 'PCT', title: "Тромбокрит", smallTitle: "", units: "%", references: { FemMin: 0.15, FemMax: 0.4, MusMin: 0.15, MusMax: 0.4 } },
            { itemType: 'RTC', title: "Ретикулоциты", smallTitle: "", units: "%", references: { FemMin: 0.2, FemMax: 1.2, MusMin: 0.2, MusMax: 1.2 } },
            { itemType: 'EOS', title: "Эозинофилы", smallTitle: "", units: "%", references: { FemMin: 0, FemMax: 5, MusMin: 0, MusMax: 5 } },
            { itemType: 'PN', title: "Палочкоядерные нейтр.", smallTitle: "", units: "%", references: { FemMin: 1, FemMax: 6, MusMin: 1, MusMax: 6 } },
            { itemType: 'SN', title: "Сегментоядерные нейтр.", smallTitle: "", units: "%", references: { FemMin: 47, FemMax: 72, MusMin: 47, MusMax: 72 } },
            { itemType: 'lymph%', title: "Лимфациты (отн)", smallTitle: "", units: "%", references: { FemMin: 18, FemMax: 40, MusMin: 18, MusMax: 40 } },
            { itemType: 'MON', title: "Моноциты", smallTitle: "", units: "%", references: { FemMin: 2, FemMax: 9, MusMin: 2, MusMax: 9 } },
            { itemType: 'ELSp', title: "СОЭ", smallTitle: "", units: "сек", references: { FemMin: 2, FemMax: 15.0, MusMin: 1, MusMax: 10 } },
          ]
        },
        {
          title: 'Общий анализ мочи', srcList: [
            { itemType: 'LEU', title: "Лейкоциты", smallTitle: "", units: "кл.в п/з", references: { FemMin: 0, FemMax: 6, MusMin: 0, MusMax: 3 } },
            { itemType: 'KET', title: "Кетоны", smallTitle: "", units: "ммоль/л", references: { FemMin: 0, FemMax: 0, MusMin: 0, MusMax: 0 } },
            { itemType: 'URO', title: "Уробилиноген", smallTitle: "", units: "ммоль/л", references: { FemMin: 0, FemMax: 0, MusMin: 0, MusMax: 0 } },
            { itemType: 'BIL', title: "Билирубин", units: "ммоль/л", references: { FemMin: 0, FemMax: 0, MusMin: 0, MusMax: 0 } },
            { itemType: 'PRO', title: "Белок", smallTitle: "", units: "гр/л", references: { FemMin: 0, FemMax: 0.033, MusMin: 0, MusMax: 0.033 } },
            { itemType: 'GLU', title: "Глюкоза", smallTitle: "", units: "ммоль/л", references: { FemMin: 0, FemMax: 0.8, MusMin: 0, MusMax: 0.8 } },
            { itemType: 'SG', title: "Плотность", smallTitle: "", units: "", references: { FemMin: 1012, FemMax: 1022, MusMin: 1012, MusMax: 1022 } },
            { itemType: 'BLD', title: "Эритроциты", smallTitle: "", units: "кл. в п/з", references: { FemMin: 0, FemMax: 3, MusMin: 0, MusMax: 3 } },
            { itemType: 'pH', title: "Кислотность", smallTitle: "", units: "", references: { FemMin: 4, FemMax: 7, MusMin: 4, MusMax: 7 } },
            { itemType: 'cells', title: "Эп. клетки", smallTitle: "", units: "кл.в п/з", references: { FemMin: 0, FemMax: 10, MusMin: 0, MusMax: 10 } },
          ]
        },
        {
          title: 'Биохимический анализ крови', srcList: [
            { itemType: 'CBIL', title: "Общ. билирубин", smallTitle: "", units: "мкмоль/л", references: { FemMin: 8.5, FemMax: 20.5, MusMin: 8.5, MusMax: 20.5 } },
            { itemType: 'RELBIL', title: "Связанный билирубин", smallTitle: "", units: "мкмоль/л", references: { FemMin: 2.2, FemMax: 5.1, MusMin: 2.2, MusMax: 5.1 } },
            { itemType: 'NRELBIL', title: "Несвязанный билирубин", smallTitle: "", units: "мкмоль/л", references: { FemMin: 0, FemMax: 17.1, MusMin: 0, MusMax: 17.1 } },
            { itemType: 'ThymPro', title: "Тимоловая проба", smallTitle: "", units: "ед.", references: { FemMin: 0, FemMax: 5, MusMin: 0, MusMax: 5 } },
            { itemType: 'CPRO', title: "Общ. белок", smallTitle: "", units: "гр/л", references: { FemMin: 65, FemMax: 85, MusMin: 65, MusMax: 85 } },
            { itemType: 'ALB', title: "Альбумин", smallTitle: "", units: "гр/л", references: { FemMin: 35, FemMax: 50, MusMin: 35, MusMax: 50 } },
            { itemType: 'AST', title: "АСТ", smallTitle: "", units: "ЕД/л", references: { FemMin: 5, FemMax: 40, MusMin: 5, MusMax: 40 } },
            { itemType: 'ALT', title: "АЛТ", smallTitle: "", units: "ЕД/л", references: { FemMin: 5, FemMax: 40, MusMin: 5, MusMax: 40 } },
            { itemType: 'GGT', title: "ГГТ", smallTitle: "", units: "ЕД/л", references: { FemMin: 0, FemMax: 32, MusMin: 0, MusMax: 50 } },
            { itemType: 'ChelPhosph', title: "ЩФ", smallTitle: "", units: "ЕД/л", references: { FemMin: 64, FemMax: 306, MusMin: 80, MusMax: 306 } },
            { itemType: 'LypCom', title: "Общ. ХС", smallTitle: "", units: "ммоль/л", references: { FemMin: 0, FemMax: 5.0, MusMin: 0, MusMax: 5.0 } },
            { itemType: 'BLypPro', title: "b-Липопротеиды", smallTitle: "", units: "ед", references: { FemMin: 35, FemMax: 55, MusMin: 35, MusMax: 55 } },
            { itemType: 'LypHigh', title: "ХС ЛПВП", smallTitle: "", units: "ммоль/л", references: { FemMin: 1.2, FemMax: 999, MusMin: 1, MusMax: 999 } },
            { itemType: 'LypLow', title: "ХС ЛПНП", smallTitle: "", units: "ммоль/л", references: { FemMin: 0, FemMax: 1.4, MusMin: 0, MusMax: 1.4 } },
            { itemType: 'LypTG', title: "ТГ", smallTitle: "", units: "ммоль/л", references: { FemMin: 0, FemMax: 1.7, MusMin: 0, MusMax: 1.7 } },
            { itemType: 'URI', title: "Мочевина", smallTitle: "", units: "ммоль/л", references: { FemMin: 1.7, FemMax: 8.3, MusMin: 1.7, MusMax: 8.3 } },
            { itemType: 'CREAT', title: "Креатинин", smallTitle: "", units: "ммоль/л", references: { FemMin: 53, FemMax: 97, MusMin: 61, MusMax: 115 } },
            { itemType: 'PTI', title: "ПТИ", smallTitle: "", units: "%", references: { FemMin: 80, FemMax: 105, MusMin: 80, MusMax: 105 } },
            { itemType: 'Phybr', title: "Фибриноген", smallTitle: "", units: "гр/л", references: { FemMin: 1.8, FemMax: 4.0, MusMin: 1.8, MusMax: 4.0 } },
            { itemType: 'CProt', title: "СРБ", smallTitle: "", units: "мг/л", references: { FemMin: 0, FemMax: 6.0, MusMin: 0, MusMax: 6.0 } },
            { itemType: 'RevmFact', title: "РФ", smallTitle: "", units: "МЕ/мл", references: { FemMin: 0, FemMax: 8.0, MusMin: 0, MusMax: 8.0 } },
            { itemType: 'URIAC', title: "Мочевая кислота", smallTitle: "", units: "ЕД/л", references: { FemMin: 140, FemMax: 320, MusMin: 200, MusMax: 420 } },
            { itemType: 'AMYL', title: "Амилаза", smallTitle: "", units: "ЕД/л", references: { FemMin: 25, FemMax: 125, MusMin: 25, MusMax: 125 } },
            { itemType: 'Ca', title: "Кальций", smallTitle: "", units: "ммольл", references: { FemMin: 2.02, FemMax: 2.55, MusMin: 2.02, MusMax: 2.55 } },
            { itemType: 'PH', title: "Фосфор", smallTitle: "", units: "ммоль/л", references: { FemMin: 0.87, FemMax: 1.45, MusMin: 0.87, MusMax: 1.45 } },
            { itemType: 'K', title: "Калий", smallTitle: "", units: "ммоль/л", references: { FemMin: 3.5, FemMax: 5.0, MusMin: 3.5, MusMax: 5.0 } },
            { itemType: 'NA', title: "Натрий", smallTitle: "", units: "ммоль/л", references: { FemMin: 135, FemMax: 145, MusMin: 135, MusMax: 145 } },
            { itemType: 'GLU', title: "Глюкоза", smallTitle: "", units: "ммоль/л", references: { FemMin: 0, FemMax: 6.1, MusMin: 0, MusMax: 6.1 } }
          ]
        }
      ],
      Results: [],
    }
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

    GenerateFinalText() {
      let _separator = '';
      let _resultStrings = [];
      let _thereIsNoResults = true;
      switch (this.finalTextMode) {
        case 'full_col': _separator = '\n'; break;
        case 'full_line': _separator = ', '; break;
      }

      for (let index = 0; index < this.Results.length; index++) {
        const _analys = this.Results[index];
        let _labItemFinalstrings = [];
        if (_analys.results.length > 0) {
          const _titleNdate = `${(index == 0 || _thereIsNoResults) ? '' : '\n'}${_analys.title} от ${_analys.date}:\n`;
          _thereIsNoResults = false;

          for (let j = 0; j < _analys.results.length; j++) {
            const labItem = _analys.results[j];
            const _ref = this.GetReference(labItem.item);
            let _finalString = `${labItem.item.title}: ${labItem.value} ${this.use_units ? labItem.item.units : ''} ${this.use_reference ? _ref : ''}`;
            _labItemFinalstrings.push(_finalString.trim());
          }
          _resultStrings.push(_titleNdate + _labItemFinalstrings.join(_separator));
        }
      }
      return _resultStrings.join('\n');
    },

    CopyFinalText() {
      navigator.clipboard.writeText(this.finalText);
    },
    GetReference(item) {
      if (this.mode === 'muscle') {
        return `(${item.references.MusMin}-${item.references.MusMax})`;
      }
      else if (this.mode === 'femini') {
        return `(${item.references.FemMin}-${item.references.FemMax})`;
      }
    }
  },
  
  computed: {
    finalText() {
      return this.GenerateFinalText();
    }
  },
  created() {
    for (let index = 0; index < this.AnalysList.length; index++) {
      const element = this.AnalysList[index];
      let _item = { title: element.title, results: [], date: '' };
      this.Results.push(_item);

    }
  }
}
</script>

<style scoped>
* {
  font-family: 'Ubuntu', sans-serif;
}

.tables {
  width: 700px;
}

.table {
  border: 2px solid #666666;
  border-collapse: collapse;
  width: 100%;
}

th {
  border-bottom: 1px solid #ddd;
}

.forms {
  display: flex;
  align-items: start;
  justify-content: start;
  flex-direction: column;
  width: 555px;
  position: fixed;
  right: 0;
  bottom: 0;
  top: 0;
}

.controls {
  width: 100%;
}

.final {
  text-wrap: wrap;
}
</style>