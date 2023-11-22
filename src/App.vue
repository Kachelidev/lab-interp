<template>
  <nav>
    <div class="nav-item" v-for="(item, index) in navs" :key="index">
      <input type="radio" :id="index" :value="index" v-model="selected">
      <label :for="index">{{ index == 0 ? 'Все' : AnalysList[index - 1].small_title }}</label>
    </div>
  </nav>
  <div class="tables">

    <AnalysList v-for="(   analys, index   ) in    AnalysList   " :analysTitle="analys.title" :srcArray="analys.srcList"
      v-model="Results[index]" v-show="selected == 0 || selected == index + 1" />
  </div>

  <div class="forms">
    <div class="controls">
      <button type="button" class="btn-copy" @click="CopyFinalText">Скопировать текст</button>
      <div class="additional-controls">
        <label>Расположение </label>
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
    </div>
    <pre class="final">{{ finalText }}</pre>
  </div>
</template>

<script>
import AnalysList from './components/AnalysList.vue';

export default {
  components: { AnalysList },
  data() {
    return {
      selected: 0,
      finalTextMode: 'full_col',
      mode: 'muscle',
      use_units: false,
      use_reference: false,
      AnalysList: [
        {
          title: 'Общий анализ крови', small_title: 'ОАК', srcList: [
            { itemType: 'WBC', title: "Лейкоциты", units: "10^9/л", references: { FemMin: 4, FemMax: 9, MusMin: 4, MusMax: 9 } },
            { itemType: 'HGB', title: "Гемоглобин", units: "гр/л", references: { FemMin: 120, FemMax: 140, MusMin: 130, MusMax: 160 } },
            { itemType: 'RBC', title: "Эритроциты", units: "10^12/л", references: { FemMin: 3.9, FemMax: 4.7, MusMin: 4.0, MusMax: 5.0 } },
            { itemType: 'HCT', title: "Гематокрит", units: "%", references: { FemMin: 0.85, FemMax: 1.15, MusMin: 0.85, MusMax: 1.15 } },
            // { itemType: 'MCV', title: "Ср. объем эритроцита",  units: "фЛ", references: { FemMin: 78, FemMax: 94, MusMin: 78, MusMax: 94 } },
            // { itemType: 'MCH', title: "Ср. сод. гемоглобина в эр.",  units: "пгр", references: { FemMin: 26, FemMax: 32, MusMin: 26, MusMax: 32 } },
            // { itemType: 'MCHC', title: "Ср.конц-я гемоглобина в эр.",  units: "гр/л", references: { FemMin: 32.2, FemMax: 36.4, MusMin: 32.2, MusMax: 36.4 } },
            // { itemType: 'RDW-CV', title: "Отклонение р-ра эритроцита",  units: "%", references: { FemMin: 11.6, FemMax: 14.8, MusMin: 11.6, MusMax: 14.8 } },
            // { itemType: 'RDW-SD', title: "Разброс размеров  эр.",  units: "фЛ", references: { FemMin: 35.3, FemMax: 49.9, MusMin: 35.3, MusMax: 49.9 } },
            { itemType: 'PLT', title: "Тромбоциты", units: "10^9/л", references: { FemMin: 180, FemMax: 320, MusMin: 180, MusMax: 320 } },
            // { itemType: 'MPV', title: "Ср. объем тромбоцита",  units: "фЛ", references: { FemMin: 7.4, FemMax: 10.4, MusMin: 7.4, MusMax: 10.4 } },
            // { itemType: 'PDW', title: "Ширина распр-я тромбоцитов",  units: "", references: { FemMin: 9.4, FemMax: 18.1, MusMin: 9.4, MusMax: 18.1 } },
            // { itemType: 'PCT', title: "Тромбокрит",  units: "%", references: { FemMin: 0.15, FemMax: 0.4, MusMin: 0.15, MusMax: 0.4 } },
            // { itemType: 'RTC', title: "Ретикулоциты",  units: "%", references: { FemMin: 0.2, FemMax: 1.2, MusMin: 0.2, MusMax: 1.2 } },
            { itemType: 'EOS', title: "Эозинофилы", units: "%", references: { FemMin: 0, FemMax: 5, MusMin: 0, MusMax: 5 } },
            { itemType: 'PN', title: "Палочкоядерные нейтр.", units: "%", references: { FemMin: 1, FemMax: 6, MusMin: 1, MusMax: 6 } },
            { itemType: 'SN', title: "Сегментоядерные нейтр.", units: "%", references: { FemMin: 47, FemMax: 72, MusMin: 47, MusMax: 72 } },
            { itemType: 'lymph%', title: "Лимфациты (отн)", units: "%", references: { FemMin: 18, FemMax: 40, MusMin: 18, MusMax: 40 } },
            { itemType: 'MON', title: "Моноциты", units: "%", references: { FemMin: 2, FemMax: 9, MusMin: 2, MusMax: 9 } },
            { itemType: 'ELSp', title: "СОЭ", units: "сек", references: { FemMin: 2, FemMax: 15.0, MusMin: 1, MusMax: 10 } },
          ]
        },
        {
          title: 'Общий анализ мочи', small_title: 'ОАМ', srcList: [
            { itemType: 'LEU', title: "Лейкоциты", units: "кл.в п/з", references: { FemMin: 0, FemMax: 6, MusMin: 0, MusMax: 3 } },
            { itemType: 'KET', title: "Кетоны", units: "ммоль/л", references: { FemMin: 0, FemMax: 0, MusMin: 0, MusMax: 0 } },
            { itemType: 'URO', title: "Уробилиноген", units: "ммоль/л", references: { FemMin: 0, FemMax: 0, MusMin: 0, MusMax: 0 } },
            { itemType: 'BIL', title: "Билирубин", units: "ммоль/л", references: { FemMin: 0, FemMax: 0, MusMin: 0, MusMax: 0 } },
            { itemType: 'PRO', title: "Белок", units: "гр/л", references: { FemMin: 0, FemMax: 0.033, MusMin: 0, MusMax: 0.033 } },
            { itemType: 'GLU', title: "Глюкоза", units: "ммоль/л", references: { FemMin: 0, FemMax: 0.8, MusMin: 0, MusMax: 0.8 } },
            { itemType: 'SG', title: "Плотность", units: "", references: { FemMin: 1012, FemMax: 1022, MusMin: 1012, MusMax: 1022 } },
            { itemType: 'BLD', title: "Эритроциты", units: "кл. в п/з", references: { FemMin: 0, FemMax: 3, MusMin: 0, MusMax: 3 } },
            { itemType: 'pH', title: "Кислотность", units: "", references: { FemMin: 4, FemMax: 7, MusMin: 4, MusMax: 7 } },
            { itemType: 'cells', title: "Эп. клетки", units: "кл.в п/з", references: { FemMin: 0, FemMax: 10, MusMin: 0, MusMax: 10 } },
          ]
        },
        {
          title: 'Биохимический анализ крови', small_title: 'БхАК', srcList: [
            { itemType: 'CBIL', title: "Общ. билирубин", units: "мкмоль/л", references: { FemMin: 8.5, FemMax: 20.5, MusMin: 8.5, MusMax: 20.5 } },
            { itemType: 'RELBIL', title: "Прямой билирубин", units: "мкмоль/л", references: { FemMin: 2.2, FemMax: 5.1, MusMin: 2.2, MusMax: 5.1 } },
            { itemType: 'NRELBIL', title: "Непрямой билирубин", units: "мкмоль/л", references: { FemMin: 0, FemMax: 17.1, MusMin: 0, MusMax: 17.1 } },
            { itemType: 'ThymPro', title: "Тимоловая проба", units: "ед.", references: { FemMin: 0, FemMax: 5, MusMin: 0, MusMax: 5 } },
            { itemType: 'CPRO', title: "Общ. белок", units: "гр/л", references: { FemMin: 65, FemMax: 85, MusMin: 65, MusMax: 85 } },
            { itemType: 'ALB', title: "Альбумин", units: "гр/л", references: { FemMin: 35, FemMax: 50, MusMin: 35, MusMax: 50 } },
            { itemType: 'AST', title: "АСТ", units: "ЕД/л", references: { FemMin: 5, FemMax: 40, MusMin: 5, MusMax: 40 } },
            { itemType: 'ALT', title: "АЛТ", units: "ЕД/л", references: { FemMin: 5, FemMax: 40, MusMin: 5, MusMax: 40 } },
            { itemType: 'GGT', title: "ГГТ", units: "ЕД/л", references: { FemMin: 0, FemMax: 32, MusMin: 0, MusMax: 50 } },
            { itemType: 'ChelPhosph', title: "ЩФ", units: "ЕД/л", references: { FemMin: 64, FemMax: 306, MusMin: 80, MusMax: 306 } },
            { itemType: 'LypCom', title: "Общ. ХС", units: "ммоль/л", references: { FemMin: 0, FemMax: 5.0, MusMin: 0, MusMax: 5.0 } },
            // { itemType: 'BLypPro', title: "b-Липопротеиды",  units: "ед", references: { FemMin: 35, FemMax: 55, MusMin: 35, MusMax: 55 } },
            { itemType: 'LypHigh', title: "ХС ЛПВП", units: "ммоль/л", references: { FemMin: 1.2, FemMax: 999, MusMin: 1, MusMax: 999 } },
            { itemType: 'LypLow', title: "ХС ЛПНП", units: "ммоль/л", references: { FemMin: 0, FemMax: 1.4, MusMin: 0, MusMax: 1.4 } },
            { itemType: 'LypTG', title: "ТГ", units: "ммоль/л", references: { FemMin: 0, FemMax: 1.7, MusMin: 0, MusMax: 1.7 } },
            { itemType: 'URI', title: "Мочевина", units: "ммоль/л", references: { FemMin: 1.7, FemMax: 8.3, MusMin: 1.7, MusMax: 8.3 } },
            { itemType: 'CREAT', title: "Креатинин", units: "ммоль/л", references: { FemMin: 53, FemMax: 97, MusMin: 61, MusMax: 115 } },
            { itemType: 'CProt', title: "СРБ", units: "мг/л", references: { FemMin: 0, FemMax: 6.0, MusMin: 0, MusMax: 6.0 } },
            { itemType: 'RevmFact', title: "РФ", units: "МЕ/мл", references: { FemMin: 0, FemMax: 8.0, MusMin: 0, MusMax: 8.0 } },
            { itemType: 'URIAC', title: "Мочевая кислота", units: "ЕД/л", references: { FemMin: 140, FemMax: 320, MusMin: 200, MusMax: 420 } },
            { itemType: 'AMYL', title: "Амилаза", units: "ЕД/л", references: { FemMin: 25, FemMax: 125, MusMin: 25, MusMax: 125 } },
            { itemType: 'Ca', title: "Кальций", units: "ммольл", references: { FemMin: 2.02, FemMax: 2.55, MusMin: 2.02, MusMax: 2.55 } },
            { itemType: 'PH', title: "Фосфор", units: "ммоль/л", references: { FemMin: 0.87, FemMax: 1.45, MusMin: 0.87, MusMax: 1.45 } },
            { itemType: 'K', title: "Калий", units: "ммоль/л", references: { FemMin: 3.5, FemMax: 5.0, MusMin: 3.5, MusMax: 5.0 } },
            { itemType: 'NA', title: "Натрий", units: "ммоль/л", references: { FemMin: 135, FemMax: 145, MusMin: 135, MusMax: 145 } },
            { itemType: 'GLU', title: "Глюкоза", units: "ммоль/л", references: { FemMin: 0, FemMax: 6.1, MusMin: 0, MusMax: 6.1 } }
          ]
        },
        {
          title: 'Коагулограмма', small_title: 'КОА', srcList: [
            { itemType: 'PTI', title: "ПТИ", units: "сек", references: { FemMin: 13, FemMax: 18, MusMin: 13, MusMax: 18 } },
            { itemType: 'Phybr', title: "Фибриноген", units: "гр/л", references: { FemMin: 1.8, FemMax: 4.0, MusMin: 1.8, MusMax: 4.0 } },
            { itemType: 'Tbl', title: "Длительность кровотечения", units: "сек", references: { FemMin: 0, FemMax: 240, MusMin: 0, MusMax: 240 } },
            { itemType: 'Tcon', title: "Время свертывания", units: "сек", references: { FemMin: 30, FemMax: 300, MusMin: 30, MusMax: 300 } },
            { itemType: 'INV', title: "МНО", units: "", references: { FemMin: 0.85, FemMax: 1.15, MusMin: 0.85, MusMax: 1.15 } },
            { itemType: 'ATPT', title: "АПТВ", units: "сек", references: { FemMin: 24, FemMax: 34, MusMin: 24, MusMax: 34 } },
          ]
        },
        {
          title: 'Гормоны ЩЖ', small_title: 'ГрмЩЖ', srcList: [
            { itemType: 'T3s', title: "Т3 св", units: "нмоль/л", references: { FemMin: 2.5, FemMax: 5.8, MusMin: 2.5, MusMax: 5.8 } },
            { itemType: 'T4s', title: "Т4 св", units: "нмоль/л", references: { FemMin: 11.5, FemMax: 23, MusMin: 11.5, MusMax: 23 } },
            { itemType: 'TTG', title: "ТТГ", units: "мМЕ/л", references: { FemMin: 0.17, FemMax: 4.7, MusMin: 0.17, MusMax: 4.7 } },
            { itemType: 'ATPO', title: "Ат к ТПО", units: "", references: { FemMin: 0, FemMax: 50, MusMin: 0, MusMax: 50 } },
          ]
        },
      ],
      Results: [],
      navs: [0]
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
    },
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
      this.navs.push(index + 1);
    }
  }
}
</script>

<style scoped>
* {
  font-family: 'Ubuntu', sans-serif;
}

nav {
  position: fixed;
  width: 90px;
  background: #d5e2ec;
  left: 0;
  bottom: 0;
  top: 0;
  box-shadow: 5px 0px 10px #666666;
  padding-top: 25px;
}

.nav-item {
  margin-left: 16px;
}

.nav-item input[type=radio] {
  display: none;
}

.nav-item label {
  cursor: pointer;
  line-height: 34px;
  user-select: none;
}

.nav-item label:hover {
  color: #666666;
}

.nav-item input[type=radio]:checked+label {
  text-decoration: underline #427cae 3px;
}

.tables {
  width: 700px;
  margin-left: 100px;
}

th {
  border-bottom: 1px solid #ddd;
}

.forms {
  display: flex;
  flex-direction: column;
  justify-content: start;
  align-items: center;
  width: 500px;
  position: fixed;
  right: 10px;
  bottom: 0;
  top: 0;
  box-shadow: -5px 0px 10px #666666;
}

.controls {
  width: 100%;
  display: flex;
  flex-direction: row;
  justify-content: center;
}

.btn-copy {
  display: inline-block;
  box-sizing: border-box;
  padding: 0 20px;
  margin: 15px;
  outline: none;
  border: none;
  border-radius: 4px;
  height: 32px;
  line-height: 32px;
  font-size: 14px;
  font-weight: 500;
  text-decoration: none;
  color: #000;
  background-color: #abc0e6;
  box-shadow: 0 2px #598bd1;
  cursor: pointer;
  user-select: none;
  appearance: none;
  touch-action: manipulation;
}

.btn-copy:hover {
  transition: 0.25s;
  background-color: #598bd1;
}

.btn-copy:active {
  background-color: #2f599e !important;
}

.additional-controls {
  display: flex;
  flex-direction: column;
  justify-content: start;
}

.final {
  width: 90%;
  text-wrap: wrap;
  min-height: 150px;
  box-shadow: 2px 2px 6px inset #666;
}
</style>