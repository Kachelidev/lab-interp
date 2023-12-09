<template>
  <nav>
    <div class="nav-items">
      <div class="nav-item" :class="{ 'selected': index == selected }" v-for="(item, index) in navs" :key="index">
        <input type="radio" :id="index" :value="index" v-model="selected">
        <label :for="index">{{ index == 0 ? 'Все' : AnalysList[index - 1].small_title }}</label>
      </div>
    </div>
    <!-- <div class="question"></div> -->
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
          <div>
            <input type="checkbox" name="" id="useAbbreviature" v-model="useAbbrev" />
            <label for="useAbbreviature">Сокращенные названия</label>
          </div>
        </div>
      </div>
    </div>
    <pre class="final">{{ finalText }}</pre>
    <MassIndexComponent />
    <ClierenceSpeedComponent :creatininValue="creatinin" v-if="creatinin >= 0" :gender="mode" />
  </div>
</template>

<script>
import AnalysList from '@/components/AnalysList.vue';
import ClierenceSpeedComponent from '@/components/ClierenseSpeedComponent.vue';
import MassIndexComponent from '@/components/MassIndexComponent.vue';

export default {
  components: { AnalysList, ClierenceSpeedComponent, MassIndexComponent },
  data() {
    return {
      useAbbrev: true,
      selected: 0,
      finalTextMode: 'full_col',
      mode: 'muscle',
      use_units: false,
      use_reference: false,
      AnalysList: [
        {
          title: 'Общий анализ крови', small_title: 'ОАК', srcList: [
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
          title: 'Общий анализ крови развернутый', small_title: 'ОАКр', srcList: [
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
        {
          title: 'Общий анализ мочи', small_title: 'ОАМ', srcList: [
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
          title: 'Биохимический анализ крови', small_title: 'БхАК', srcList: [
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
            { itemType: 'GLU', title: "Глюкоза", abrreviature: 'Глюк', units: "ммоль/л", references: { FemMin: 0, FemMax: 6.1, MusMin: 0, MusMax: 6.1 } },
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
          title: 'Коагулограмма', small_title: 'КОА', srcList: [
            { itemType: 'PTI', title: "Протромбиновый индекс", abrreviature: 'ПТИ', units: "%", references: { FemMin: 50, FemMax: 150, MusMin: 50, MusMax: 150 } },
            { itemType: 'Phybr', title: "Фибриноген", abrreviature: 'Фибр', units: "гр/л", references: { FemMin: 1.8, FemMax: 4.0, MusMin: 1.8, MusMax: 4.0 } },
            { itemType: 'Tbl', title: "Длительность кровотечения", abrreviature: 'Длит.кров', units: "сек", references: { FemMin: 0, FemMax: 240, MusMin: 0, MusMax: 240 } },
            { itemType: 'Tcon', title: "Время свертывания", abrreviature: 'Вр.сверт', units: "сек", references: { FemMin: 30, FemMax: 300, MusMin: 30, MusMax: 300 } },
            { itemType: 'INV', title: "Международное нормализованное отношение", abrreviature: 'МНО', units: "", references: { FemMin: 0.85, FemMax: 1.15, MusMin: 0.85, MusMax: 1.15 } },
            { itemType: 'ATPT', title: "Активированное протромбиновое время", abrreviature: 'АПТВ', units: "сек", references: { FemMin: 24, FemMax: 34, MusMin: 24, MusMax: 34 } },
          ]
        },
        {
          title: 'Гормоны ЩЖ', small_title: 'ГрмЩЖ', srcList: [
            { itemType: 'T3s', title: "Т3 свободный", abrreviature: 'Т3 св', units: "нмоль/л", references: { FemMin: 2.5, FemMax: 5.8, MusMin: 2.5, MusMax: 5.8 } },
            { itemType: 'T4s', title: "Т4 свободный", abrreviature: 'Т4 св', units: "нмоль/л", references: { FemMin: 11.5, FemMax: 23, MusMin: 11.5, MusMax: 23 } },
            { itemType: 'TTG', title: "Тиреотропный гормон", abrreviature: 'ТТГ', units: "мМЕ/л", references: { FemMin: 0.17, FemMax: 4.7, MusMin: 0.17, MusMax: 4.7 } },
            { itemType: 'ATPO', title: "Атитела к ТПО", abrreviature: 'Ат к ТПО', units: "", references: { FemMin: 0, FemMax: 50, MusMin: 0, MusMax: 50 } },
            { itemType: 'PARAT', title: "Паратгормон", abrreviature: 'Паратг.', units: "пг/мл", references: { FemMin: 4.7, FemMax: 117, MusMin: 4.7, MusMax: 117 } },
          ]
        },
      ],
      Results: [],
      navs: [0],
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
            const _analysTitle = this.useAbbrev ? labItem.item.abrreviature : labItem.item.title;
            const _analysUnits = (!labItem.item.hasOwnProperty('isLiteral') && this.use_units && !labItem.item.isLiteral) ? labItem.item.units : '';
            const _analysRefs = (!labItem.item.hasOwnProperty('isLiteral') && this.use_reference && !labItem.item.isLiteral) ? this.GetReference(labItem.item) : '';
            let _finalString = `${_analysTitle}: ${labItem.value} ${_analysUnits} ${_analysRefs}`;
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
    },
    creatinin() {
      const _ID = this.Results[3].results.findIndex(x => x.item.itemType == 'CREAT');
      return _ID >= 0 ? this.Results[3].results[_ID].value : -1;
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
  font-family: 'Sofia Sans Semi Condensed', sans-serif;
}

nav {
  position: fixed;
  width: 90px;
  background: #ffffff;
  left: 0;
  bottom: 0;
  top: 0;
  display: flex;
  justify-content: space-between;
  flex-direction: column;
  align-items: center;
  /* box-shadow: 5px 0px 10px #666666; */
  padding-top: 25px;
  margin: 5px;
  border-radius: 5px;
}

.nav-items {
  width: 100%;
}

.nav-item {
  width: 100%;
  padding-left: 16px;
}

.nav-item input[type=radio] {
  display: none;
}

.nav-item label {
  cursor: pointer;
  line-height: 34px;
  user-select: none;
}

.nav-item.selected {
  border-left: 4px solid #427cae;
  font-weight: bold;
}

.nav-item label:hover {
  color: #666666;
}

/* .nav-item input[type=radio]:checked+label {
  text-decoration: underline #427cae 0px;
} */

.question {
  border: solid 2px #427cae;
  border-radius: 100%;
  width: 48px;
  height: 48px;
  display: flex;
  align-items: center;
  justify-content: center;
}

.question::before {
  content: '?';
  font-size: 32px;
  color: #427cae;
}

.question:hover {
  border: solid 4px #427cae;
}

.tables {
  width: 700px;
  margin-left: 100px;

}

th {
  border-bottom: 1px solid #ddd;
}

.forms {
  background: #ffffff;
  display: flex;
  flex-direction: column;
  justify-content: start;
  align-items: center;
  width: 500px;
  position: fixed;
  right: 10px;
  bottom: 0;
  top: 0;
  margin: 5px;
  border-radius: 5px;
  /* box-shadow: -5px 0px 10px #666666; */
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
  max-height: 70%;
  overflow: auto;
  padding: 10px;
  border: rgb(65, 65, 65) 1px solid;
  border-radius: 8px;
  margin-top: 15px;
  background: #f8f8f8;
  /* box-shadow: 2px 2px 6px inset #666; */
}
</style>