<template>
  <div class="container">
    <section class="section">
      <h1>Попробуйте новый удобный виджет подсказок по организациям</h1>
      <ul class='benefits-list'>
        <li class='benefits-list__item'>настраиваемый внешний вид и кастомизация</li>
        <li class='benefits-list__item'>мгновенное заполнение всей нужной информации</li>
        <li class='benefits-list__item'>ищет компании и индивидуальных предпринимателей</li>
      </ul>
    </section>
    <section class="section">
      <h2>Демонстрация</h2>
      <p>Начните вводить значение в поле и выберите вариант из списка, чтобы увидеть полные данные в форме результатов
      </p>
      <div class="filters">
        <label>
          <input class="checkbox" hidden type="checkbox" v-model="hideresult" true-value="true" false-value="false">
          <div class="customCheckbox" />
          Скрыть форму с результатами до выбора значения
        </label>
        <label>
          <input class="customTogglerCheckbox" hidden type="checkbox" v-model="shouldChangeFields">
          <div class="customToggler" />
          Изменить поля результирующей формы
        </label>
        <div class="themeOptions">
          <label>
            <input class="radio" hidden type="radio" value="standart" v-model="styling">
            <div class="customRadio" />
            Cтанадртное оформление
          </label>
          <label>
            <input class="radio" hidden type="radio" value="light" v-model="styling">
            <div class="customRadio" />
            Светлая тема
          </label>
          <label>
            <input class="radio" hidden type="radio" value="dark" v-model="styling">
            <div class="customRadio" />
            Темная тема
          </label>
        </div>
      </div>
    </section>
    <section class="section demo-container">
      <div class="demo">
        <dadata-form v-bind="getStyles" :hideresult="hideresult" id="form"
          apikey="4af749c38de44c5dd8683061dab5231d80a7c1f5">
          <template v-if="useCustomFields">
            <template v-for="(value, name) in customFields">
              <label :slot="name" :for="name">
                {{ value.label }}
              </label>
              <input v-if="value.isInput" :slot="name" :id="name" class="result" :placeholder="value.inputPlaceholder"
                type="text">
              <span class="result" :slot="name" v-else>
                {{ value.inputPlaceholder }}
              </span>
            </template>
          </template>
        </dadata-form>
        <div class="changeFieldsForm" v-if="shouldChangeFields">
          <h3>Измените настройки для результирующих полей:</h3>
          <form @submit.prevent="handleChangeFields">
            <template v-for="value in customFields">
              <fieldset>
                <legend>Настройки для поля "{{ value.label }}"</legend>
                <label>
                  Введите лейбл для поля
                  <input type="text" v-model="value.label">
                </label>
                <label class="checkboxLabel">
                  <input type="checkbox" v-model="value.isInput">
                  Выводить input вместо span для результата
                </label>
                <label v-if="value.isInput">
                  Введите плейсхолдер для поля
                  <input type="text" v-model="value.inputPlaceholder">
                </label>
              </fieldset>
            </template>
            <input type="submit" value="Сохранить" class="changeFieldsFormSubmitBtn">
            <button @click="cancelChangeFields" class="changeFieldsFormCancelBtn">Отменить</button>
          </form>
        </div>
      </div>
      <div class="results">
        <div class="formData">
          <h3>Заполненные данные из формы</h3>
          <template v-if="formDataExists">
            <p v-for="(value, name) in formData">
              <span class="formData__name">{{ name }}:</span>
              <span>{{ value || 'не указано' }}</span>
            </p>
          </template>
          <p v-else>Введите или измените текст в полях формы с результатами, чтобы увидеть данные</p>
        </div>
        <div class="fullResponse">
          <h3>Полные данные об организации</h3>
          <div v-if="fullResponseExists" class="responseContainer">
            <template v-for="(value, name) in fullResponse">
              <span class="responseContainerText" v-html="name + ': ' + getSubValues(value)" />
            </template>
          </div>
          <p v-else>Выберите вариант из списка, чтобы увидеть данные</p>
        </div>
      </div>
    </section>
  </div>
</template>

<script>
export default {
  name: 'IndexPage',
  data: function () {
    return {
      hideresult: 'true',
      formData: {},
      fullResponse: {},
      customFields: {},
      customFieldsChecked: false,
      styling: "standart",
    }
  },
  computed: {
    fullResponseExists: function () {
      return Object.keys(this.fullResponse).length
    },
    formDataExists: function () {
      return Object.keys(this.formData).length
    },
    getStyles: function () {
      if (this.styling === 'standart') return {};

      let hoststyle, inputStyle, inputHoverStyle,
        inputFocusStyle, inputPlaceholderStyle, inputFilledStyle,
        suggestionsContainerStyle, suggestionsTitleStyle;

      const errorStyle = `
          color: #f43e3e;
        `;

      if (this.styling === 'dark') {
        hoststyle = `
          background-color: #222831;
          color: #F9F9F9;
          padding: 25px;
        `;
        inputStyle = `
          background: transparent;
          color: #F9F9F9;
          border-color: rgba(244, 62, 62, 0.3);
        `;
        inputHoverStyle = `
          border-color: rgba(244, 62, 62, 0.5);
        `;
        inputFocusStyle = `
          border-color: rgba(244, 62, 62, 0.7);
        `;
        inputFilledStyle = `
          border-color: rgba(244, 62, 62, 0.7);
        `;
        inputPlaceholderStyle = `
          color: rgba(249, 249, 249, 0.8);
        `;
        suggestionsContainerStyle = `
          background: #2C3440;
        `;
        suggestionsTitleStyle = `
          color: rgba(249, 249, 249, 0.8);
        `;
      } else {
        hoststyle = `
          background-color: #FAFAFA;
          color: #000000;
          padding: 25px;
        `;
        inputStyle = `
          background: transparent;
          color: #000000;
          border-color: rgba(40, 40, 70, 0.1);
        `;
        inputHoverStyle = `
          border-color: rgba(40, 40, 70, 0.3);
        `;
        inputFocusStyle = `
          border-color: rgba(40, 40, 70, 0.5);
        `;
        inputFilledStyle = `
          border-color: rgba(40, 40, 70, 0.5);
        `;
        inputPlaceholderStyle = `
          color: rgba(40, 40, 70, 0.3);
        `;
        suggestionsContainerStyle = `
          background: #FFFFFF;
        `;
        suggestionsTitleStyle = `
          color: rgba(40, 40, 70, 0.3);
        `;
      }

      return {
        hoststyle,
        inputStyle,
        inputHoverStyle,
        inputFocusStyle,
        inputPlaceholderStyle,
        errorStyle,
        suggestionsContainerStyle,
        suggestionsTitleStyle,
        inputFilledStyle
      }
    },
    shouldChangeFields: {
      get: function () {
        return this.customFieldsChecked;
      },
      set: function (newValue) {
        this.customFieldsChecked = newValue;
        if (newValue) {
          this.hideresult = 'true';
          this.customFields = {
            short_name: {
              label: 'Краткое название',
              isInput: false,
              inputPlaceholder: 'Введите краткое наименование'
            },
            full_name: {
              label: 'Полное наименование',
              isInput: true,
              inputPlaceholder: 'Введите полное наименование'
            },
            inn_kpp: {
              label: 'ИНН / КПП',
              isInput: true,
              inputPlaceholder: 'Введите инн / кпп'
            },
            address: {
              label: 'Адрес',
              isInput: true,
              inputPlaceholder: 'Введите адрес'
            }
          }
        } else {
          this.customFields = {};
        }
      }
    },
    useCustomFields: function () {
      return Object.keys(this.customFields).length
    }
  },
  methods: {
    setFullResponse: function (e) {
      this.fullResponse = e.detail.data;
    },
    setFormData: function (e) {
      this.formData = e.detail.data;
    },
    getSubValues: function (value) {
      if (typeof value === 'object' && value) {
        return Object.entries(value).reduce((acc, [key, val]) => {
          acc += `<span class="subValue">${key}: ${this.getSubValues(val)}</span>`;
          return acc;
        }, '<span class="subValue">') + '</span>';
      } else return `<span>${value}</span>`;
    },
    handleChangeFields: function (e) {
      this.customFieldsChecked = false;
      this.hideresult = 'false';
    },
    cancelChangeFields: function () {
      this.shouldChangeFields = false;
    }
  },
  mounted: function () {
    document.getElementById('form').addEventListener('dadata_value_changed', this.setFullResponse);
    document.getElementById('form').addEventListener('dadata_value_manually_changed', this.setFormData);
  },
  beforeDestroy: function () {
    document.getElementById('form').removeEventListener('dadata_value_changed', this.setFullResponse);
    document.getElementById('form').removeEventListener('dadata_value_manually_changed', this.setFormData);
  },
}
</script>

<style>

@import 'normalize.css/normalize.css';
@import url('https://fonts.googleapis.com/css2?family=Roboto:wght@400;700&display=swap');

html,
body {
  min-width: 320px;
  max-width: 100vw;
  min-height: 100vh;
  overflow-x: hidden;
}

body {
  font-family: 'Roboto', Tahoma, Geneva, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #000000;
  position: relative;
}

* {
  box-sizing: border-box;
}

.container {
  max-width: 1440px;
  padding-left: 120px;
  padding-right: 120px;
  margin-left: auto;
  margin-right: auto;
}

.section {
  padding-top: 50px;
}

.benefits-list {
  margin: 50px 0 0;
  padding: 0;
  display: flex;
  gap: 30px;
}

.benefits-list__item {
  list-style: none;
  flex: 1 1 0;
  padding: 15px;
  display: flex;
  align-items: center;
  justify-content: center;
  border-radius: 8px;
  background-color: #f43e3e;
  color: white;
}

.filters {
  margin-top: 35px;
  display: flex;
  gap: 30px;
  align-items: flex-start;
}

.filters label,
.themeOptions label {
  flex: 1 1 auto;
  display: flex;
  gap: 10px;
  align-items: flex-start;
  cursor: pointer;
}

.themeOptions {
  display: flex;
  flex-direction: column;
  gap: 10px;
}

.customToggler {
  flex: 0 0 50px;
  display: block;
  height: 30px;
  background-color: rgba(121, 121, 121, 0.1);
  position: relative;
  transition: .2s;
}

.customToggler::after {
  content: "";
  position: absolute;
  left: 5px;
  top: 5px;
  height: calc(100% - 10px);
  aspect-ratio: 1;
  display: block;
  background-color: rgb(185, 185, 185);
  transition: .2s;
}

.customTogglerCheckbox:checked~.customToggler {
  background-color: #f43e3e;
}

.customTogglerCheckbox:checked~.customToggler:after {
  right: 5px;
  left: unset;
  background-color: white;
}

.checkbox:checked~.customCheckbox:after,
.radio:checked~.customRadio:after {
  opacity: 1;
}

.customCheckbox,
.customRadio {
  flex: 0 0 25px;
  aspect-ratio: 1;
  vertical-align: middle;
  border: 1px solid gray;
  color: #f43e3e;
  position: relative;
}

.customRadio {
  border-radius: 50%;
}

.customCheckbox::after,
.customRadio::after {
  content: '\2713';
  opacity: 0;
  position: absolute;
  left: 50%;
  top: 50%;
  transform: translate(-50%, -50%);
  transition: .2s;
}

.customCheckbox::after {
  content: '\2713';
}

.customRadio::after {
  content: '\2BC3';
}

.demo-container {
  display: flex;
  gap: 30px;
  justify-content: space-between;
  align-items: flex-start;
  margin-bottom: 50px;
}

.demo,
.results {
  flex: 1 1 50%;
}

.results {
  border-radius: 8px;
  padding: 30px;
  border: 1px solid gray;
}

.results h3 {
  margin-top: 0;
}

.fullResponse {
  margin-top: 50px;
}

.responseContainer {
  display: flex;
  flex-direction: column;
  gap: 5px;
  max-width: 100%;
  background-color: #f5f5f5;
  border-radius: 0.25rem;
  color: #333333;
  font-size: 0.8rem;
  line-height: 1.3;
  padding: 0.5rem 1rem;
}

.responseContainerText {
  word-break: break-all;
}

.subValue {
  display: block;
  margin-top: 5px;
  margin-left: 15px;
}

.formData__name {
  font-weight: 700;
}

.changeFieldsForm form {
  display: flex;
  flex-direction: column;
  gap: 15px;
}

.changeFieldsForm fieldset {
  display: flex;
  flex-direction: column;
  gap: 15px;
  padding: 20px;
}

.changeFieldsForm label:not(.checkboxLabel) {
  display: flex;
  flex-direction: column;
  gap: 5px;
}

.changeFieldsFormSubmitBtn,
.changeFieldsFormCancelBtn {
  border: none;
  outline: none;
  padding: 10px 15px;
  color: white;
  display: block;
  cursor: pointer;
  transition: .2s;
}

.changeFieldsFormSubmitBtn:hover,
.changeFieldsFormCancelBtn:hover {
  opacity: 0.8;
}

.changeFieldsFormSubmitBtn {
  background-color: #f43e3e;
}

.changeFieldsFormCancelBtn {
  background-color: dimgray;
}

@media (max-width: 1200px) {
  .container {
    padding-left: 80px;
    padding-right: 80px;
  }
}

@media (max-width: 768px) {
  .container {
    padding-left: 40px;
    padding-right: 40px;
  }

  .demo-container,
  .filters,
  .benefits-list {
    flex-wrap: wrap;
  }

  .subValue {
    margin-left: 3px;
  }
}

@media (max-width: 600px) {
  .container {
    padding-left: 20px;
    padding-right: 20px;
  }
}

</style>
