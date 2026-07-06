# Implementation Plan: number-to-uah-words

## Overview

Реалізація односторінкового веб-додатку (HTML + CSS + JS в одному файлі) для перетворення числових значень у текстове представлення суми в українських гривнях. Імплементація побудована інкрементально: спочатку базова структура та дані, потім ядро конвертера, далі UI та інтеграція.

## Tasks

- [x] 1. Створити HTML-структуру та CSS-стилі
  - [x] 1.1 Створити файл index.html з базовою HTML-структурою, CSS-стилями та заглушками для JS
    - Створити HTML5 document з meta viewport для адаптивності
    - Додати заголовок з назвою додатку та описом
    - Створити Поле_введення (input type="text", maxlength=16)
    - Створити Кнопку_перетворення
    - Створити Область_результату з візуальним відокремленням (рамка/фон)
    - Додати CSS для адаптивного дизайну (320px–1920px без горизонтальної прокрутки)
    - _Requirements: 7.1, 7.2, 7.3, 7.4, 7.5, 1.1_

- [x] 2. Реалізувати Data Models та допоміжні функції
  - [x] 2.1 Додати lookup-таблиці та функцію decline
    - Додати константи: ONES_MASCULINE, ONES_FEMININE, TEENS, TENS, HUNDREDS
    - Додати SCALE_WORDS, HRYVNIA_FORMS, KOPIYKA_FORMS
    - Реалізувати функцію `decline(n, forms)` згідно з дизайном
    - _Requirements: 2.9, 3.1, 3.2, 3.3, 4.3, 4.4, 4.5, 6.4_

  - [ ]* 2.2 Написати property-тест для функції decline
    - **Property 3: Declension selects correct grammatical form**
    - **Validates: Requirements 2.9, 3.1, 3.2, 3.3, 4.3, 4.4, 4.5, 6.4**

- [x] 3. Реалізувати Validator та Number Splitter
  - [x] 3.1 Реалізувати функцію validateInput
    - Перевірка на порожній рядок → "Введіть числове значення"
    - Перевірка на недопустимі символи → "Введіть коректне числове значення"
    - Перевірка на від'ємне число → "Значення повинно бути додатнім"
    - Перевірка на перевищення максимуму → "Значення занадто велике"
    - Заміна коми на крапку, перевірка на не більше одного розділювача
    - Повернення { valid, value, error }
    - _Requirements: 1.2, 1.4, 1.5, 1.6, 8.1, 8.2, 8.3, 8.4_

  - [ ]* 3.2 Написати property-тест для validateInput
    - **Property 1: Validation correctly classifies input**
    - **Validates: Requirements 1.2, 1.4, 1.5, 1.6, 8.2**

  - [x] 3.3 Реалізувати функцію splitNumber
    - Відокремити цілу частину від дробової
    - Обчислити копійки з округленням Math.round(fractionalPart * 100)
    - Повернення { integerPart, koppiiky }
    - _Requirements: 1.3, 4.6_

  - [ ]* 3.4 Написати property-тест для splitNumber
    - **Property 2: Rounding preserves two-decimal precision**
    - **Validates: Requirements 1.3, 4.6**

- [x] 4. Checkpoint
  - Ensure all tests pass, ask the user if questions arise.

- [x] 5. Реалізувати конвертер числа в слова
  - [x] 5.1 Реалізувати функцію groupToWords
    - Обробити сотні, десятки, одиниці
    - Підтримати параметр gender ("masculine" | "feminine")
    - Для тисяч — жіночий рід: "одна", "дві"
    - _Requirements: 2.3, 2.4, 2.5, 2.6, 2.8_

  - [ ]* 5.2 Написати property-тест для feminine gender у тисячах
    - **Property 4: Feminine gender agreement for thousands**
    - **Validates: Requirements 2.8**

  - [x] 5.3 Реалізувати функцію integerToWords
    - Обробити нуль як "Нуль"
    - Розбити число на групи: мільярди, мільйони, тисячі, одиниці
    - Для кожної групи викликати groupToWords з правильним родом
    - Додати розрядне слово у правильній формі через decline
    - Пропустити нульові групи, уникати подвійних пробілів
    - Перша літера велика
    - _Requirements: 2.1, 2.2, 2.7, 2.8, 2.9, 2.10_

  - [ ]* 5.4 Написати property-тест для zero-group omission
    - **Property 6: Zero-group omission in word representation**
    - **Validates: Requirements 2.7**

- [x] 6. Реалізувати Formatter та вихідний формат
  - [x] 6.1 Реалізувати функцію formatResult
    - Сформувати рядок: "{hryvniWords} {гривня/гривні/гривень} {DD} {копійка/копійки/копійок}"
    - Копійки з провідним нулем (padStart(2, '0'))
    - Перша літера велика
    - _Requirements: 6.1, 6.2, 6.3, 6.5, 4.1, 4.2, 4.7, 3.4_

  - [ ]* 6.2 Написати property-тест для output format
    - **Property 5: Output format structure**
    - **Validates: Requirements 6.1, 6.2, 6.3, 4.1, 4.7, 3.4**

  - [ ]* 6.3 Написати unit-тести для конвертера
    - Тест нуля: 0 → "Нуль гривень 00 копійок"
    - Тест одиниць: 1 → "Одна гривня 00 копійок", 5 → "П'ять гривень 00 копійок"
    - Тест teens: 11 → "Одинадцять гривень 00 копійок"
    - Тест тисяч (жіночий рід): 1000, 2000
    - Тест мільйонів: 1000000
    - Тест складених: 1000001
    - Тест копійок: 0.01, 0.99
    - Тест округлення: 1.005
    - Тест максимуму: 999999999999.99
    - _Requirements: 2.1–2.9, 3.1–3.4, 4.1–4.7, 6.1–6.5_

- [x] 7. Checkpoint
  - Ensure all tests pass, ask the user if questions arise.

- [x] 8. Реалізувати UI Event Handler та інтеграцію
  - [x] 8.1 Реалізувати функцію copyToClipboard та handleConvert
    - Реалізувати async copyToClipboard з try/catch та поверненням boolean
    - Реалізувати handleConvert: зчитати input → validateInput → конвертація → відображення → копіювання → очищення
    - При помилці валідації: відобразити помилку, не копіювати, не очищувати поле
    - При помилці копіювання: показати результат + повідомлення про неможливість копіювання
    - Прив'язати обробник до Кнопки_перетворення (addEventListener)
    - _Requirements: 5.1, 5.2, 5.3, 5.4, 5.5, 5.6, 5.7, 8.5, 8.6_

  - [ ]* 8.2 Написати unit-тести для UI інтеграції
    - Тест: натискання з валідним введенням → результат у DOM + clipboard called + input cleared
    - Тест: натискання з невалідним введенням → помилка у DOM + clipboard NOT called + input unchanged
    - Тест: clipboard rejection → результат показаний + повідомлення
    - _Requirements: 5.2, 5.3, 5.4, 5.7, 8.5, 8.6_

- [x] 9. Final checkpoint
  - Ensure all tests pass, ask the user if questions arise.

## Notes

- Tasks marked with `*` are optional and can be skipped for faster MVP
- Each task references specific requirements for traceability
- Checkpoints ensure incremental validation
- Property tests validate universal correctness properties from the design document
- Unit tests validate specific examples and edge cases
- All code lives in a single index.html file (HTML + CSS + JS)
- Tests use Vitest + fast-check and import functions from the main file or a separate module for testability

## Task Dependency Graph

```json
{
  "waves": [
    { "id": 0, "tasks": ["1.1", "2.1"] },
    { "id": 1, "tasks": ["2.2", "3.1", "3.3"] },
    { "id": 2, "tasks": ["3.2", "3.4", "5.1"] },
    { "id": 3, "tasks": ["5.2", "5.3"] },
    { "id": 4, "tasks": ["5.4", "6.1"] },
    { "id": 5, "tasks": ["6.2", "6.3"] },
    { "id": 6, "tasks": ["8.1"] },
    { "id": 7, "tasks": ["8.2"] }
  ]
}
```
