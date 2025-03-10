---
id: 2257
title: MinusOne
lang: uk
level: medium
tags: math
---

## Завдання

Ви отримуєте на вході число(завжди позитивне).
Ваш тип повинен повернути те ж число, тільки на одиницю менше.
Наприклад:

```typescript
type Zero = MinusOne<1>; // 0
type FiftyFour = MinusOne<55>; // 54
```

## Розв'язок

Ця задача дійсно доволі складна.
TypeScript не може нічого запропонувати для роботи з числами - нічого!

Тому, ми повинні якось найти обхідний шлях для реалізації такої математично операції.
І, зрозумійте, що це навряд чи буде відмінне рішення і використовувати його як приклад для наслідування навряд чи вийде.

Я почав з того, що запитав себе.
Чи були у нас випадки, коли ми працювали з числовими літералами, але без використання самих літералів.
І, як виявилося, так.
Ми використовували кортежі.

В нас уже були задачі, в яких ми працювали з кортежами й дізнавалися довжину цих кортежів.
Пам'ятаєте синтаксис для цього?
Це було дуже давно - ми зверталися до властивості `length`, яка й повертала нам числовий тип літерал.

Тому я подумав, а що якщо ми створимо кортеж потрібної нам довжини і виведемо його частину без останнього елементу.
А після, візьмемо довжину цього кортежу.

Давайте почнемо з допоміжного типу, який буде нам створювати кортеж потрібної довжини.
Назвемо його `Tuple`:

```typescript
type Tuple<L extends number, T extends unknown[] = []> = never;
```

Він приймає в якості аргументів довжину кортежу і тимчасовий акумулятор.
Цей акумулятор буде накопичувати кортеж, поки ми не отримаємо кортеж потрібної довжини.
Щоб реалізувати цю перевірку, звернемося до властивості `length` й порівняємо його з необхідним:

```typescript
type Tuple<L extends number, T extends unknown[] = []> = T["length"] extends L
  ? never
  : never;
```

Як тільки ми отримаємо кортеж потрібної довжини - повертаємо його:

```typescript
type Tuple<L extends number, T extends unknown[] = []> = T["length"] extends L
  ? T
  : never;
```

Але, якщо ж ми не отримаємо кортеж потрібної довжини, нам потрібно додати до нього ще один елемент.
І продовжувати так варто доти, поки не отримаємо очікувану довжину кортежу:

```typescript
type Tuple<L extends number, T extends unknown[] = []> = T["length"] extends L
  ? T
  : Tuple<L, [...T, unknown]>;
```

Тепер, коли ми викличемо наший тип з параметром `5`, наприклад, то ми отримаємо кортеж довжиною в 5 елементів й типу `unknown`.
Якщо ж ми звернемося до властивості `length` на цьому кортежі, то ми отримаємо числовий літерал - `5`.
Те що й потрібно було.

Як же дістати літерал `4` з такого кортежу?
Вивести кортеж, довжина якого 5, але без останнього елементу.
Іншими словами, кортеж буде коротший на один елемент.

```typescript
type MinusOne<T extends number> = Tuple<T> extends [...infer L, unknown]
  ? never
  : never;
```

Використовуючи таку конструкцію, ми отримаємо в тип параметрі `L` кортеж без останнього цього елементу.
Все що залишається зробити - це повернути довжину виведеного кортежу.

```typescript
type MinusOne<T extends number> = Tuple<T> extends [...infer L, unknown]
  ? L["length"]
  : never;
```

Таким чином, ми реалізували якусь подобу математичної операції в системі типів.
Наприклад, викликаючи наший тип з параметром `5`, ми отримаємо числовий літерал `4`.

```typescript
type Tuple<L extends number, T extends unknown[] = []> = T["length"] extends L
  ? T
  : Tuple<L, [...T, unknown]>;
type MinusOne<T extends number> = Tuple<T> extends [...infer L, unknown]
  ? L["length"]
  : never;
```

Але!
Велике "але"!
В останніх версіях TypeScript, вони додали перевірку на кількість рекурсивних викликів.
Тому, якщо чесно, ми не пройдемо тести, в яких використовуються числа більше 50.
Так що цей розв'язок складно назвати розв'язком.

Якщо у вас є кращі ідеї, не соромтеся, пишіть їх з поясненням в коментарях нижче.
Дякую!

## Посилання

- [Дженерики](https://www.typescriptlang.org/docs/handbook/2/generics.html)
- [Індексні типи](https://www.typescriptlang.org/docs/handbook/2/indexed-access-types.html)
- [Умовні типи](https://www.typescriptlang.org/docs/handbook/2/conditional-types.html)
- [Виведення в умовних типах](https://www.typescriptlang.org/docs/handbook/2/conditional-types.html#inferring-within-conditional-types)
- [Рекурсивні умовні типи](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-4-1.html#recursive-conditional-types)
- [Варіативні типи](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-4-0.html#variadic-tuple-types)
