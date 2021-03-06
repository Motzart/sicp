# Challenge: Program without variables #javascript
# Задача написание программы без использования переменных
Небольшое испытание - написать небольшой кусок приложения(что то что реально работает и будет полезно),  но без использования переменных.

Я планирую создать библиотеку для работы с Promise Я был вдохновлен на это статьёй которую я нашол - [Trey Huffine](https://medium.com/@treyhuffine), [Learn JavaScript Promises by Building a Promise from Scratch](https://medium.com/gitconnected/understand-javascript-promises-by-building-a-promise-from-scratch-84c0fd855720).

Я думаю Promise достаточно просты для понимая кода, и в тоже время достаточно комплексные что бы быть испытанием

Прваила:
1.Не использовать Var, let, const, import, class, ну и как бонус не использовать конструкции if, switch, function.
2.Библиотеки разрешены если они удовлетворяют первое правило
3.Новые библиотеки могут быть созданы если они удовлетворяют текущие правила
4.библиотеки должны быть достаточно обшими для использования в любом проекте и не могут быть использованы как бизнес логика созданного кода
5.Тесты не важны,  но если вы решите писать тесты тогда они не подчиняются правилам
## Начнем
TDD 
Promise довольно сложная библиотека, и если я делаю изменения в коде, я должен быть уверен, изменения ничего не полагают что работало ранее. Потому я собираюсь выписать все мои тесты изначально.

Одно замечание, я не планирую использование классов поскольку считаю что классы в js не нужны, Поетому вместо  обычного кода которые вы предпочитаете использовать при создании Promise : new Promise((resolve, reject)), Вы можете написать просто XPromise((resolve, reject)), без оператора new.

## Начнем с интерфейса
Прямо сейчас я презентую свой челендж таск. Аналогично реализации  Promise A+ , я хочу иметь возможность создавать промисы используя XPromise((resolve, reject) => …), Promise.resolve(…) и Promise.reject(). И так XPromise должен быть функцией и должна иметь два аргумента resolve и reject которые тоже в свою очередь должны быть функциями.

Обычно это не сложно и не занимает много времени, но если следовать правилам я не могу сделать что-то подобное

```javascript
// Invalid: Breaks the rules
const XPromise = () => {}
XPromise.resolve = () => {}
XPromise.reject = () => {}

export default XPromise 
```

Время проявить креативность используя Object.assign() что бы добавить resolve и reject к главной функции

```
// Good: Follows the rules!
export default Object.assign(
  () => {},
  {
    resolve: () => {},
    reject: () => {},
  }
)
```

Пока я доволен этим, Но я не реализовал еще rejaect и resolve - функции хелперы которые в итоге должны быть связаны с  XPromise функцией, которая на данные момент не имеет с ними связи.

## Создание связей без переменных
XPromise  также должен вернуть объект которые содержит две функции then, catch. Эти функции должны вызывать оригинальную XPromise функцию, которые опять таки не связаны.
```
export default Object.assign(
  () => {
    return {
      // uh oh. can't reference XPromise anymore!
      then: () => XPromise(/* ... */),
      catch: () => XPromise(/* ... */),
    }
  },
  {
    resolve: () => {},
    reject: () => {},
  }
)
```

И так, я должен узнать как создать асинхронную рекурсивную и анонимную функцию иначе все это будет бесполезно.

## Время Комбинаторов
Когда мы говорим об анонимной рекурсивной функции знаменитый Y Combinator сразу преходит в голову, Какая цель Y Combinator,  хоть он и не единственный комбинатор который мы можем использовать. Для этого таска я принял решение использовать U Combinator.
Он прост в запоминании:
`f=>f(f)`
Это все                                        