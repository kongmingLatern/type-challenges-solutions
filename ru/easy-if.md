---
id: 268
title: If
lang: ru
level: easy
tags: utils
---

## Проблема

Реализовать вспомогательный тип `If`.
Он принимает условие `C` и типы `T` и `F`.
Если условие `C` правдивое, верните тип `T`, иначе - `F`.
Например:

```typescript
type A = If<true, 'a', 'b'> // expected to be 'a'
type B = If<false, 'a', 'b'> // expected to be 'b'
```

## Решение

Проблема очень простая и подсказка на её решение уже скрывается в формулировке.
Для того чтобы реализовать тип `If`, нам нужно использовать условные типы.
Если условие `C` правдивое, то возвращаем тип `T`, иначе - `F`.

```typescript
type If<C, T, F> = C extends true ? T : F;
```

Но с таким решением мы получим ошибку компиляции.
TypeScript-у не нравится, что тип `C` пытаются присвоить к логическому типу.
У нас нету никаких ограничений, которые бы указали на то, что `C` это логический тип.

Починим эту ошибку, добавив `extends boolean` к тип параметру `C`.

```typescript
type If<C extends boolean, T, F> = C extends true ? T : F;
```

## Что почитать

- [Условные типы](https://www.typescriptlang.org/docs/handbook/advanced-types.html#conditional-types)