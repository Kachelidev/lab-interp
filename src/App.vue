<template>
  <nav>
    <div class="nav-items">
      <div class="nav-item" :class="{ 'selected': index == selected }" v-for="(item, index) in navs" :key="index">
        <input type="radio" :id="index" :value="index" v-model="selected">
        <label :for="index">{{ index == 0 ? 'Все' : AnalysList[index - 1].small_title }}</label>
      </div>
    </div>
    <div class="result-btn" @click="showModal()">Результат</div>
  </nav>

  <div class="tables">
    <AnalysList v-for="(   analys, index   ) in    AnalysList   " :analysTitle="analys.title" :srcArray="analys.srcList"
      v-model="Results[index]" v-show="selected == 0 || selected == index + 1" />
  </div>

  <ResultFrame v-show="show_modal" @onHide="show_modal = false" ref="modal" :results="Results" :gender="mode" />
</template>

<script>
import AnalysList from '@/components/AnalysList.vue';
import ResultFrame from '@/components/ResultFrame.vue';


export default {
  components: { AnalysList, ResultFrame },
  data() {
    return {
      selected: 0,
      mode: 'muscle',
      AnalysList: [
        {
          title: 'Общий анализ крови', small_title: 'ОАК', id: 'cba', srcList: [
            { itemType: 'WBC', title: "Лейкоциты", abrreviature: 'WBC', units: "10^9/л", references: { FemMin: 4, FemMax: 9, MusMin: 4, MusMax: 9 } },
            { itemType: 'RBC', title: "Эритроциты", abrreviature: 'RBC', units: "10^12/л", references: { FemMin: 3.9, FemMax: 4.7, MusMin: 4.0, MusMax: 5.0 } },
            { itemType: 'HGB', title: "Гемоглобин", abrreviature: 'HGB', units: "гр/л", references: { FemMin: 120, FemMax: 140, MusMin: 130, MusMax: 160 } },
            { itemType: 'HCT', title: "Гематокрит", abrreviature: 'HCT', units: "%", references: { FemMin: 0.85, FemMax: 1.15, MusMin: 0.85, MusMax: 1.15 } },
            { itemType: 'PLT', title: "Тромбоциты", abrreviature: 'PLT', units: "10^9/л", references: { FemMin: 180, FemMax: 320, MusMin: 180, MusMax: 320 } },
            { itemType: 'EOS', title: "Эозинофилы", abrreviature: 'Э', units: "%", references: { FemMin: 0, FemMax: 5, MusMin: 0, MusMax: 5 } },
            { itemType: 'PN', title: "Палочкоядерные нейтр.", abrreviature: 'П', units: "%", references: { FemMin: 1, FemMax: 6, MusMin: 1, MusMax: 6 } },
            { itemType: 'SN', title: "Сегментоядерные нейтр.", abrreviature: 'С', units: "%", references: { FemMin: 47, FemMax: 72, MusMin: 47, MusMax: 72 } },
            { itemType: 'lymph%', title: "Лимфациты (отн)", abrreviature: 'ЛФ', units: "%", references: { FemMin: 18, FemMax: 40, MusMin: 18, MusMax: 40 } },
            { itemType: 'MON', title: "Моноциты", units: "%", abrreviature: 'М', references: { FemMin: 2, FemMax: 9, MusMin: 2, MusMax: 9 } },
            { itemType: 'ELSp', title: "СОЭ", abrreviature: 'СОЭ', units: "сек", references: { FemMin: 2, FemMax: 15.0, MusMin: 1, MusMax: 10 } },
          ]
        },
        {
          title: 'Общий анализ мочи', small_title: 'ОАМ', id: 'cua', srcList: [
            { itemType: 'Col', title: "Цвет", abrreviature: 'Цв', isLiteral: true, units: "", references: { FemMin: 0, FemMax: 0, MusMin: 0, MusMax: 0 } },
            { itemType: 'LEUnum', title: "Лейкоциты (кач)", abrreviature: 'Л', isLiteral: true, units: "кл.в п/з", references: { FemMin: 0, FemMax: 6, MusMin: 0, MusMax: 3 } },
            { itemType: 'LEUlit', title: "Лейкоциты (кол)", abrreviature: 'Л', units: "кл.в п/з", references: { FemMin: 0, FemMax: 6, MusMin: 0, MusMax: 3 } },
            { itemType: 'KET', title: "Кетоны", abrreviature: 'Кет', isLiteral: true, units: "", references: { FemMin: 0, FemMax: 0, MusMin: 0, MusMax: 0 } },
            { itemType: 'URO', title: "Уробилиноген", abrreviature: 'Ур', isLiteral: true, units: "ммоль/л", references: { FemMin: 0, FemMax: 0, MusMin: 0, MusMax: 0 } },
            { itemType: 'BIL', title: "Билирубин", abrreviature: 'Бил', isLiteral: true, units: "ммоль/л", references: { FemMin: 0, FemMax: 0, MusMin: 0, MusMax: 0 } },
            { itemType: 'PROsum', title: "Белок (кач)", abrreviature: 'Прот', isLiteral: true, units: "", references: { FemMin: 0, FemMax: 0.033, MusMin: 0, MusMax: 0.033 } },
            { itemType: 'PROval', title: "Белок (кол)", abrreviature: 'Прот', units: "гр/л", references: { FemMin: 0, FemMax: 0.033, MusMin: 0, MusMax: 0.033 } },
            { itemType: 'GLU', title: "Глюкоза", abrreviature: 'Глю', isLiteral: true, units: "", references: { FemMin: 0, FemMax: 0.8, MusMin: 0, MusMax: 0.8 } },
            { itemType: 'SG', title: "Плотность", abrreviature: 'SG', units: "", references: { FemMin: 1012, FemMax: 1022, MusMin: 1012, MusMax: 1022 } },
            { itemType: 'BLDlit', title: "Эритроциты (кач)", abrreviature: 'Эр', isLiteral: true, units: "", references: { FemMin: 0, FemMax: 3, MusMin: 0, MusMax: 3 } },
            { itemType: 'BLDnum', title: "Эритроциты (кол)", abrreviature: 'Эр', units: "кл. в п/з", references: { FemMin: 0, FemMax: 3, MusMin: 0, MusMax: 3 } },
            { itemType: 'pH', title: "Кислотность", abrreviature: 'pH', units: "", references: { FemMin: 4, FemMax: 7, MusMin: 4, MusMax: 7 } },
            { itemType: 'Bak', title: "Бактерии", abrreviature: 'Бакт', isLiteral: true, units: "", references: { FemMin: 0, FemMax: 0, MusMin: 0, MusMax: 0 } },
            { itemType: 'OXA', title: "Оксалаты", abrreviature: 'Оксал', isLiteral: true, units: "", references: { FemMin: 0, FemMax: 0, MusMin: 0, MusMax: 0 } },
            { itemType: 'URA', title: "Ураты", abrreviature: 'Ур', isLiteral: true, units: "", references: { FemMin: 0, FemMax: 0, MusMin: 0, MusMax: 0 } },
            { itemType: 'PHOS', title: "Фосфаты", abrreviature: 'Фосф', isLiteral: true, units: "", references: { FemMin: 0, FemMax: 0, MusMin: 0, MusMax: 0 } },
            { itemType: 'SLIM', title: "Слизь", abrreviature: 'Слизь', isLiteral: true, units: "", references: { FemMin: 0, FemMax: 0, MusMin: 0, MusMax: 0 } },
            { itemType: 'cells', title: "Эп. клетки", abrreviature: 'Эп.кл.', isLiteral: true, units: "кл.в п/з", references: { FemMin: 0, FemMax: 10, MusMin: 0, MusMax: 10 } },
          ]
        },
        {
          title: 'Биохимический анализ крови', small_title: 'БхАК', id: 'bca', srcList: [
            { itemType: 'CBIL', title: "Общ. билирубин", abrreviature: 'Общ.бил', units: "мкмоль/л", references: { FemMin: 8.5, FemMax: 20.5, MusMin: 8.5, MusMax: 20.5 } },
            { itemType: 'RELBIL', title: "Прямой билирубин", abrreviature: 'Прям.бил', units: "мкмоль/л", references: { FemMin: 2.2, FemMax: 5.1, MusMin: 2.2, MusMax: 5.1 } },
            { itemType: 'NRELBIL', title: "Непрямой билирубин", abrreviature: 'Непр.бил', units: "мкмоль/л", references: { FemMin: 0, FemMax: 17.1, MusMin: 0, MusMax: 17.1 } },
            { itemType: 'ThymPro', title: "Тимоловая проба", abrreviature: 'Тим.пр.', units: "ед.", references: { FemMin: 0, FemMax: 5, MusMin: 0, MusMax: 5 } },
            { itemType: 'CPRO', title: "Общ. белок", abrreviature: 'О.бел', units: "гр/л", references: { FemMin: 65, FemMax: 85, MusMin: 65, MusMax: 85 } },
            { itemType: 'ALB', title: "Альбумин", abrreviature: 'Альб', units: "гр/л", references: { FemMin: 35, FemMax: 50, MusMin: 35, MusMax: 50 } },
            { itemType: 'AST', title: "АСТ", abrreviature: 'АСТ', units: "ЕД/л", references: { FemMin: 5, FemMax: 40, MusMin: 5, MusMax: 40 } },
            { itemType: 'ALT', title: "АЛТ", abrreviature: 'АЛТ', units: "ЕД/л", references: { FemMin: 5, FemMax: 40, MusMin: 5, MusMax: 40 } },
            { itemType: 'GGT', title: "ГГТ", abrreviature: 'ГГТ', units: "ЕД/л", references: { FemMin: 0, FemMax: 32, MusMin: 0, MusMax: 50 } },
            { itemType: 'ChelPhosph', title: "ЩФ", abrreviature: 'ЩФ', units: "ЕД/л", references: { FemMin: 64, FemMax: 306, MusMin: 80, MusMax: 306 } },
            { itemType: 'LypCom', title: "Общий ХС", abrreviature: 'Общ.ХС', units: "ммоль/л", references: { FemMin: 0, FemMax: 5.0, MusMin: 0, MusMax: 5.0 } },
            { itemType: 'LypHigh', title: "ХС ЛПВП", abrreviature: 'ХС ЛПВП', units: "ммоль/л", references: { FemMin: 1.2, FemMax: 999, MusMin: 1, MusMax: 999 } },
            { itemType: 'LypLow', title: "ХС ЛПНП", abrreviature: 'ХС ЛПВП', units: "ммоль/л", references: { FemMin: 0, FemMax: 1.4, MusMin: 0, MusMax: 1.4 } },
            { itemType: 'LypTG', title: "ТГ", abrreviature: 'ТГ', units: "ммоль/л", references: { FemMin: 0, FemMax: 1.7, MusMin: 0, MusMax: 1.7 } },
            { itemType: 'URI', title: "Мочевина", abrreviature: 'Моч', units: "ммоль/л", references: { FemMin: 1.7, FemMax: 8.3, MusMin: 1.7, MusMax: 8.3 } },
            { itemType: 'CREAT', title: "Креатинин", abrreviature: 'Креат', units: "мкмоль/л", references: { FemMin: 53, FemMax: 97, MusMin: 61, MusMax: 115 } },
            { itemType: 'CProt', title: "СРБ", abrreviature: 'СРБ', units: "мг/л", references: { FemMin: 0, FemMax: 6.0, MusMin: 0, MusMax: 6.0 } },
            { itemType: 'RevmFact', title: "Ревматойидный фактор", abrreviature: 'РФ', units: "МЕ/мл", references: { FemMin: 0, FemMax: 8.0, MusMin: 0, MusMax: 8.0 } },
            { itemType: 'URIAC', title: "Мочевая кислота", abrreviature: 'Моч.кисл', units: "ЕД/л", references: { FemMin: 140, FemMax: 320, MusMin: 200, MusMax: 420 } },
            { itemType: 'AMYL', title: "Амилаза", abrreviature: 'Амил', units: "ЕД/л", references: { FemMin: 25, FemMax: 125, MusMin: 25, MusMax: 125 } },
            { itemType: 'Ca', title: "Кальций", abrreviature: 'Ca', units: "ммольл", references: { FemMin: 2.02, FemMax: 2.55, MusMin: 2.02, MusMax: 2.55 } },
            { itemType: 'K', title: "Калий", abrreviature: 'K', units: "ммоль/л", references: { FemMin: 3.5, FemMax: 5.0, MusMin: 3.5, MusMax: 5.0 } },
            { itemType: 'NA', title: "Натрий", abrreviature: 'Na', units: "ммоль/л", references: { FemMin: 135, FemMax: 145, MusMin: 135, MusMax: 145 } },
            { itemType: 'GLU', title: "Глюкоза", abrreviature: 'Глю', units: "ммоль/л", references: { FemMin: 0, FemMax: 6.1, MusMin: 0, MusMax: 6.1 } },
            { itemType: 'PSA', title: "Простатспецифический антиген", abrreviature: 'ПСА', units: "нг/мл", references: { FemMin: 0, FemMax: 4.5, MusMin: 0, MusMax: 4.5 } },
            { itemType: 'Ferrit', title: "Ферритин", abrreviature: 'Феррит.', units: "мкг/л", references: { FemMin: 22, FemMax: 180, MusMin: 30, MusMax: 310 } },
            { itemType: 'TransFerr', title: "Трансферрин", abrreviature: 'ТрансФер.', units: "г/л", references: { FemMin: 2, FemMax: 3.6, MusMin: 2, MusMax: 3.6 } },
            { itemType: 'CFCA', title: "Общая железосвязывающая способность сыворотки", abrreviature: 'ОЖСС', units: "мкмоль/л", references: { FemMin: 44, FemMax: 72, MusMin: 44, MusMax: 72 } },
            { itemType: 'SivFE', title: "Сывороточное железо", abrreviature: 'Сыв.Fe', units: "мкмоль/л", references: { FemMin: 5.83, FemMax: 34.5, MusMin: 5.83, MusMax: 34.5 } },
            { itemType: '25OH', title: "Витамин D", abrreviature: '25(OH)D', units: "нмоль/л", references: { FemMin: 40, FemMax: 100, MusMin: 40, MusMax: 100 } },
            { itemType: 'FolAcid', title: "Фолиевая кислота", abrreviature: 'Фол.кисл.', units: "нг/мл", references: { FemMin: 3.1, FemMax: 20.5, MusMin: 3.1, MusMax: 20.5 } },
            { itemType: 'VitB12', title: "Витамин B12(цианокабалмин)", abrreviature: 'B12', units: "пг/мл", references: { FemMin: 191, FemMax: 663, MusMin: 191, MusMax: 663 } },
          ]
        },
        {
          title: 'Коагулограмма', small_title: 'КОА', id: 'coa', srcList: [
            { itemType: 'PTI', title: "Протромбиновый индекс", abrreviature: 'ПТИ', units: "%", references: { FemMin: 50, FemMax: 150, MusMin: 50, MusMax: 150 } },
            { itemType: 'Phybr', title: "Фибриноген", abrreviature: 'Фибр', units: "гр/л", references: { FemMin: 1.8, FemMax: 4.0, MusMin: 1.8, MusMax: 4.0 } },
            { itemType: 'Tbl', title: "Длительность кровотечения", abrreviature: 'Длит.кров', units: "сек", references: { FemMin: 0, FemMax: 240, MusMin: 0, MusMax: 240 } },
            { itemType: 'Tcon', title: "Время свертывания", abrreviature: 'Вр.сверт', units: "сек", references: { FemMin: 30, FemMax: 300, MusMin: 30, MusMax: 300 } },
            { itemType: 'INV', title: "Международное нормализованное отношение", abrreviature: 'МНО', units: "", references: { FemMin: 0.85, FemMax: 1.15, MusMin: 0.85, MusMax: 1.15 } },
            { itemType: 'ATPT', title: "Активированное протромбиновое время", abrreviature: 'АПТВ', units: "сек", references: { FemMin: 24, FemMax: 34, MusMin: 24, MusMax: 34 } },
          ]
        },
        {
          title: 'Гормоны ЩЖ', small_title: 'ГрмЩЖ', id: 'hsc', srcList: [
            { itemType: 'T3s', title: "Т3 свободный", abrreviature: 'Т3 св', units: "нмоль/л", references: { FemMin: 2.5, FemMax: 5.8, MusMin: 2.5, MusMax: 5.8 } },
            { itemType: 'T4s', title: "Т4 свободный", abrreviature: 'Т4 св', units: "нмоль/л", references: { FemMin: 11.5, FemMax: 23, MusMin: 11.5, MusMax: 23 } },
            { itemType: 'TTG', title: "Тиреотропный гормон", abrreviature: 'ТТГ', units: "мМЕ/л", references: { FemMin: 0.17, FemMax: 4.7, MusMin: 0.17, MusMax: 4.7 } },
            { itemType: 'ATPO', title: "Атитела к ТПО", abrreviature: 'Ат к ТПО', units: "", references: { FemMin: 0, FemMax: 50, MusMin: 0, MusMax: 50 } },
            { itemType: 'PARAT', title: "Паратгормон", abrreviature: 'Паратг.', units: "пг/мл", references: { FemMin: 4.7, FemMax: 117, MusMin: 4.7, MusMax: 117 } },
          ]
        },
        {
          title: 'Общий анализ крови развернутый', small_title: 'ОАКр', id: 'cbaw', srcList: [
            { itemType: 'WBC', title: "Лейкоциты", abrreviature: 'WBC', units: "10^9/л", references: { FemMin: 4, FemMax: 9, MusMin: 4, MusMax: 9 } },
            { itemType: 'RBC', title: "Эритроциты", abrreviature: 'RBC', units: "10^12/л", references: { FemMin: 3.9, FemMax: 4.7, MusMin: 4.0, MusMax: 5.0 } },
            { itemType: 'HGB', title: "Гемоглобин", abrreviature: 'HGB', units: "гр/л", references: { FemMin: 120, FemMax: 140, MusMin: 130, MusMax: 160 } },
            { itemType: 'HCT', title: "Гематокрит", abrreviature: 'HCT', units: "%", references: { FemMin: 0.85, FemMax: 1.15, MusMin: 0.85, MusMax: 1.15 } },
            { itemType: 'MCV', title: "Ср. объем эритроцита", abrreviature: 'MCV', units: "фЛ", references: { FemMin: 78, FemMax: 94, MusMin: 78, MusMax: 94 } },
            { itemType: 'MCH', title: "Ср. сод. гемоглобина в эр.", abrreviature: 'MCH', units: "пгр", references: { FemMin: 26, FemMax: 32, MusMin: 26, MusMax: 32 } },
            { itemType: 'MCHC', title: "Ср.конц-я гемоглобина в эр.", abrreviature: 'MCHC', units: "гр/л", references: { FemMin: 32.2, FemMax: 36.4, MusMin: 32.2, MusMax: 36.4 } },
            { itemType: 'RDW-CV', title: "Отклонение р-ра эритроцита", abrreviature: 'RDW-CV', units: "%", references: { FemMin: 11.6, FemMax: 14.8, MusMin: 11.6, MusMax: 14.8 } },
            { itemType: 'RDW-SD', title: "Разброс размеров  эр.", abrreviature: 'RDW-SD', units: "фЛ", references: { FemMin: 35.3, FemMax: 49.9, MusMin: 35.3, MusMax: 49.9 } },
            { itemType: 'PLT', title: "Тромбоциты", abrreviature: 'PLT', units: "10^9/л", references: { FemMin: 180, FemMax: 320, MusMin: 180, MusMax: 320 } },
            { itemType: 'MPV', title: "Ср. объем тромбоцита", abrreviature: 'MPV', units: "фЛ", references: { FemMin: 7.4, FemMax: 10.4, MusMin: 7.4, MusMax: 10.4 } },
            { itemType: 'PDW', title: "Ширина распр-я тромбоцитов", abrreviature: 'PDW', units: "", references: { FemMin: 9.4, FemMax: 18.1, MusMin: 9.4, MusMax: 18.1 } },
            { itemType: 'PCT', title: "Тромбокрит", abrreviature: 'PCT', units: "%", references: { FemMin: 0.15, FemMax: 0.4, MusMin: 0.15, MusMax: 0.4 } },
            { itemType: 'RTC', title: "Ретикулоциты", abrreviature: 'RTC', units: "%", references: { FemMin: 0.2, FemMax: 1.2, MusMin: 0.2, MusMax: 1.2 } },
            { itemType: 'EOS', title: "Эозинофилы", abrreviature: 'Э', units: "%", references: { FemMin: 0, FemMax: 5, MusMin: 0, MusMax: 5 } },
            { itemType: 'PN', title: "Палочкоядерные нейтр.", abrreviature: 'П', units: "%", references: { FemMin: 1, FemMax: 6, MusMin: 1, MusMax: 6 } },
            { itemType: 'SN', title: "Сегментоядерные нейтр.", abrreviature: 'С', units: "%", references: { FemMin: 47, FemMax: 72, MusMin: 47, MusMax: 72 } },
            { itemType: 'lymph%', title: "Лимфациты (отн)", abrreviature: 'ЛФ', units: "%", references: { FemMin: 18, FemMax: 40, MusMin: 18, MusMax: 40 } },
            { itemType: 'MON', title: "Моноциты", units: "%", abrreviature: 'М', references: { FemMin: 2, FemMax: 9, MusMin: 2, MusMax: 9 } },
            { itemType: 'ELSp', title: "СОЭ", abrreviature: 'СОЭ', units: "сек", references: { FemMin: 2, FemMax: 15.0, MusMin: 1, MusMax: 10 } },
          ]
        },
      ],
      Results: [],
      navs: [0],
      show_modal: false
    }
  },
  methods: {
    showModal() {
      this.show_modal = true;
      this.$nextTick(() => {
        this.$refs.modal.focusIt();
      });
    },
    dateToString(date) {
      return (
        date.getDate() +
        '.' +
        (date.getMonth() + 1) +
        '.' +
        date.getFullYear()
      )
    },
  },
  created() {
    let res = [];
    for (let index = 0; index < this.AnalysList.length; index++) {
      const element = this.AnalysList[index];
      let _item = { title: element.title, id: element.id, results: [], date: '' };
      this.navs.push(index + 1);
      res.push(_item);
    }
    this.Results = res;
  }
}
</script>
<style scoped lang="sass">
@import './assets/styles/style.sass'

nav 
  position: sticky
  height: 48px
  top: 0
  display: flex
  justify-content: center
  flex-direction: row
  align-items: center
  margin: 5px
  .result-btn
    text-align: left
    vertical-align: middle
    background-color: #fff
    margin-left: 15px
    user-select: none
    line-height: 32px
    padding: 0 5px
    @include border
    &:hover
      background-color: #a4bed5
  .nav-items 
    @include border
    background: #ffffff
    width: 500px
    display: flex
    flex-direction: row
    justify-content: space-around
    gap: 10px
    padding: 4px 0
    .nav-item 
      text-align: center
      & input[type=radio] 
        display: none
      & label 
        cursor: pointer
        line-height: 28px
        user-select: none
        &:hover
          color: #666666 
      &.selected 
        border-bottom: 4px solid #25b7c4
        font-weight: bold

.tables 
  margin-left: 10px
  margin-right: 10px
  display: flex
  flex-direction: row
  justify-content: center
  align-items: flex-start
  flex-wrap: wrap
  th 
    @include border
</style>