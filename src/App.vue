<template>
  <div>
    <h2>Общий анализ крови</h2>
    <input type="date" v-model="blood_date" />
    <select v-model="mode">
      <option disabled value="">Пол</option>
      <option value="muscle">М</option>
      <option value="femini">Ж</option>
    </select>
    <table class="table table-bordered">
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
        <LabAnItem v-for="item in CommonBloodAnalys" :labItem="item" :key="item.itemType" :mode="mode"
          @OnValueChanged="ChangeCBAItemValue" />
      </tbody>
    </table>

    <h2>Общий анализ мочи</h2>
    <input type="date" v-model="ur_date" />
    <select v-model="mode">
      <option disabled value="">Пол</option>
      <option value="muscle">М</option>
      <option value="femini">Ж</option>
    </select>
    <table class="table table-bordered">
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
        <LabAnItem v-for="item in CommonUrineAnalys" :labItem="item" :key="item.itemType" :mode="mode"
          @OnValueChanged="ChangeCUAItemValue" />
      </tbody>
    </table>

    <h2>Биохимичский анализ крови</h2>
    <input type="date" v-model="chem_date" />
    <select v-model="mode">
      <option disabled value="">Пол</option>
      <option value="muscle">М</option>
      <option value="femini">Ж</option>
    </select>
    <table class="table table-bordered">
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
        <LabAnItem v-for="item in BioChemBloodAnalys" :labItem="item" :key="item.itemType" :mode="mode"
          @OnValueChanged="ChangeBBAItemValue" />
      </tbody>
    </table>

    <div class="forms">
      <div class="controls">
        <button type="button" class="btn btn-outline-dark" @click="GenerateFinalText">Сгенерировать текст</button>
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
import LabAnItem from './components/LabAnItem.vue';

export default {
  components: { LabAnItem },
  data() {
    return {
      blood_date: new Date().toISOString().split('T')[0],
      ur_date: new Date().toISOString().split('T')[0],
      chem_date: new Date().toISOString().split('T')[0],
      finalText: '',
      finalTextMode: 'full_col',
      mode: 'muscle',
      use_units: false,
      use_reference: false,
      CommonBloodAnalys: [
        { itemType: 'WBC', title: "Лейкоциты", smallTitle: "", units: "10^9/л", references: { FemMin: 4, FemMax: 9, MusMin: 4, MusMax: 9 } },
        { itemType: 'HGB', title: "Гемоглобин", smallTitle: "", units: "гр/л", references: { FemMin: 120, FemMax: 140, MusMin: 130, MusMax: 160 } },
        { itemType: 'RBC', title: "Эритроциты", smallTitle: "", units: "10^12/л", references: { FemMin: 3.9, FemMax: 4.7, MusMin: 4.0, MusMax: 5.0 } },
        { itemType: 'HCT', title: "Гематокрит", smallTitle: "", units: "%", references: { FemMin: 0.85, FemMax: 1.15, MusMin: 0.85, MusMax: 1.15 } },
        { itemType: 'MCV', title: "Ср. объем эритроцита'", smallTitle: "", units: "фЛ", references: { FemMin: 78, FemMax: 94, MusMin: 78, MusMax: 94 } },
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
      ],
      CommonUrineAnalys: [
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
      ],
      BioChemBloodAnalys: [
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
      ],
      CBAResult: [],
      CUAResult: [],
      BioChemBAResult: []
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

    ChangeCBAItemValue(labItem, itemValue) {

      let _CBAArray = this.CBAResult;

      if (itemValue >= 0) {
        let _itemID = _CBAArray.findIndex(x => x.item.itemType === labItem.itemType);
        if (_itemID < 0) {
          let FinalObject = {};
          FinalObject.item = labItem;
          FinalObject.value = itemValue;
          _CBAArray.push(FinalObject);
          //console.log(`Added ${labItem.itemType} with value ${itemValue}`);
        }
        else {
          _CBAArray[_itemID].value = itemValue;
          //console.log(`Changed ${this.CBAResult[_itemID].item.itemType} with value ${itemValue}`);
        }
      }
      else {
        let _itemID = _CBAArray.findIndex(x => x.item.itemType === labItem.itemType);
        if (_itemID >= 0) {
          _CBAArray.splice(_itemID, 1);
          //console.log(`Removed ${labItem.itemType}`);
        }
      }
      _CBAArray.sort((a, b) => this.CommonBloodAnalys.indexOf(a.item) - this.CommonBloodAnalys.indexOf(b.item));
      this.CBAResult = _CBAArray;
      this.GenerateFinalText();
    },

    ChangeCUAItemValue(labItem, itemValue) {
      let _CBAArray = this.CUAResult;

      if (itemValue >= 0) {
        let _itemID = _CBAArray.findIndex(x => x.item.itemType === labItem.itemType);
        if (_itemID < 0) {
          let FinalObject = {};
          FinalObject.item = labItem;
          FinalObject.value = itemValue;
          _CBAArray.push(FinalObject);
          //console.log(`Added ${labItem.itemType} with value ${itemValue}`);
        }
        else {
          _CBAArray[_itemID].value = itemValue;
          //console.log(`Changed ${this.CBAResult[_itemID].item.itemType} with value ${itemValue}`);
        }
      }
      else {
        let _itemID = _CBAArray.findIndex(x => x.item.itemType === labItem.itemType);
        if (_itemID >= 0) {
          _CBAArray.splice(_itemID, 1);
          //console.log(`Removed ${labItem.itemType}`);
        }
      }
      _CBAArray.sort((a, b) => this.CommonUrineAnalys.indexOf(a.item) - this.CommonUrineAnalys.indexOf(b.item));
      this.CUAResult = _CBAArray;
      this.GenerateFinalText();
    },

    ChangeBBAItemValue(labItem, itemValue) {
      let _CBAArray = this.BioChemBAResult;

      if (itemValue >= 0) {
        let _itemID = _CBAArray.findIndex(x => x.item.itemType === labItem.itemType);
        if (_itemID < 0) {
          let FinalObject = {};
          FinalObject.item = labItem;
          FinalObject.value = itemValue;
          _CBAArray.push(FinalObject);
          //console.log(`Added ${labItem.itemType} with value ${itemValue}`);
        }
        else {
          _CBAArray[_itemID].value = itemValue;
          //console.log(`Changed ${this.CBAResult[_itemID].item.itemType} with value ${itemValue}`);
        }
      }
      else {
        let _itemID = _CBAArray.findIndex(x => x.item.itemType === labItem.itemType);
        if (_itemID >= 0) {
          _CBAArray.splice(_itemID, 1);
          //console.log(`Removed ${labItem.itemType}`);
        }
      }
      _CBAArray.sort((a, b) => this.BioChemBloodAnalys.indexOf(a.item) - this.BioChemBloodAnalys.indexOf(b.item));
      this.BioChemBAResult = _CBAArray;
      this.GenerateFinalText();
    },

    GenerateFinalText() {
      this.finalText = '';
      if (this.finalTextMode == 'full_col') {
        this.finalText = this.GenerateFullTextCol();
      }
      else if (this.finalTextMode == 'full_line') {
        this.finalText = this.GenerateFullTextLine();
      }
    },

    GenerateFullTextCol() {
      let _stringArrays = [];

      if (this.CBAResult.length > 0) {
        _stringArrays.push('ОАК от ' + this.dateToString(new Date(this.blood_date)) + ':');

        for (let item of this.CBAResult) {
          if (item.value > 0) {
            let _ref = this.GetReference(item.item);
            let _result = `${item.item.title}: ${item.value} ${this.use_units ? item.item.units : ''} ${this.use_reference ? _ref : ''}`;
            _stringArrays.push(_result);
          }
        }
      }

      if (this.CUAResult.length > 0) {
        _stringArrays.push('\n' + 'ОАM от ' + this.dateToString(new Date(this.ur_date)) + ':');

        for (let item of this.CUAResult) {
          if (item.value > 0) {
            let _ref = this.GetReference(item.item);
            let _result = `${item.item.title}: ${item.value} ${this.use_units ? item.item.units : ''} ${this.use_reference ? _ref : ''}`;
            _stringArrays.push(_result);
          }
        }
      }

      if (this.BioChemBAResult.length > 0) {
        _stringArrays.push('\n' + 'БхАК от ' + this.dateToString(new Date(this.chem_date)) + ':');

        for (let item of this.BioChemBAResult) {
          if (item.value > 0) {
            let _ref = this.GetReference(item.item);
            let _result = `${item.item.title}: ${item.value} ${this.use_units ? item.item.units : ''} ${this.use_reference ? _ref : ''}`;
            _stringArrays.push(_result);
          }
        }
      }
      return _stringArrays.join('\n');
    },

    GenerateFullTextLine() {
      let _stringArrays = [];

      if (this.CBAResult.length > 0) {
        _stringArrays.push('ОАК от ' + this.dateToString(new Date(this.blood_date)) + ':');
        let _CBAResultString = [];
        for (let item of this.CBAResult) {
          if (item.value > 0) {
            let _ref = this.GetReference(item.item);
            let _result = `${item.item.title}: ${item.value} ${this.use_units ? item.item.units : ''} ${this.use_reference ? _ref : ''}`;
            _CBAResultString.push(_result);
          }
        }
        _stringArrays.push(_CBAResultString.join(', '));
      }

      if (this.CUAResult.length > 0) {
        _stringArrays.push('\n' + 'ОАM от ' + this.dateToString(new Date(this.ur_date)) + ':');
        let _CUAResultString = [];
        for (let item of this.CUAResult) {
          if (item.value > 0) {
            let _ref = this.GetReference(item.item);
            let _result = `${item.item.title}: ${item.value} ${this.use_units ? item.item.units : ''} ${this.use_reference ? _ref : ''}`;
            _CUAResultString.push(_result);
          }
        }
        _stringArrays.push(_CUAResultString.join(', '));
      }

      if (this.BioChemBAResult.length > 0) {
        _stringArrays.push('\n' + 'БхАК от ' + this.dateToString(new Date(this.chem_date)) + ':');
        let _BBAResultString = [];
        for (let item of this.BioChemBAResult) {
          if (item.value > 0) {
            let _ref = this.GetReference(item.item);
            let _result = `${item.item.title}: ${item.value} ${this.use_units ? item.item.units : ''} ${this.use_reference ? _ref : ''}`;
            _BBAResultString.push(_result);
          }
        }
        _stringArrays.push(_BBAResultString.join(', '));
      }
      return _stringArrays.join('\n');
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
  watch: {
    blood_date(newDate) {
      this.GenerateFinalText();
    },
    ur_date(newDate) {
      this.GenerateFinalText();
    },
    chem_date(newDate) {
      this.GenerateFinalText();
    },
    finalTextMode(newMode) {
      this.finalText = '';
      this.GenerateFinalText();
    },
    use_units(newUse) {
      this.GenerateFinalText();
    },
    use_reference(newUse) {
      this.GenerateFinalText();
    },
  }
}
</script>

<style scoped>
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