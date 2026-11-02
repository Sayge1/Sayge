
# Go — задачи 1–5

## Задача 1. Статистика по текстовым кодам

### Условие

Дан список текстовых кодов. Нужно собрать статистику: количество, число различных кодов, распределение по первому символу и самый длинный код.

**Даны:**

```go
type CodeStats struct {
    Total    int
    Unique   int
    ByPrefix map[rune]int
    Longest  string
}

var ErrEmptyCode = errors.New("пустой код")
```

**Реализуйте:**

```go
func AnalyzeCodes(codes []string) (CodeStats, error)
```

### Входные данные и результат

Для входа:

```go
[]string{"go1", "го2", "go1", "гость"}
```

Результат содержит:

```text
Total: 4
Unique: 3
ByPrefix: 'g' -> 2, 'г' -> 2
Longest: "гость"
```

- Длина кода и первый символ считаются **по рунам**, а не по байтам.
- При равной длине выбирается первый встретившийся код.
- На успешном вызове `ByPrefix` всегда инициализирована, даже для пустого списка.
- Если есть пустой код, функция возвращает **нулевую `CodeStats`** и ошибку с индексом и `ErrEmptyCode` в цепочке.

### Критерии приёмки

- Повторяющиеся строки учитываются в `Total`, но не повторяются в `Unique`.
- Кириллица обрабатывается корректно.
- Для `[]string{"ok", "", "next"}` возвращается ошибка с индексом `1`; `errors.Is(err, ErrEmptyCode)` — `true`.

> [!TIP]
> Пакет `unicode/utf8` содержит `RuneCountInString` и `DecodeRuneInString`.

---

## Задача 2. Атомарное резервирование товаров

### Условие

На складе хранится доступное количество товаров. Несколько запросов должны зарезервировать товары, но **если хоть один запрос некорректен — ни один остаток не изменяется**.

**Даны:**

```go
type Product struct {
    Name  string
    Stock int
}

type Request struct {
    Name     string
    Quantity int
}

type Store struct {
    Items map[string]*Product
}
```

**Реализуйте:**

```go
func NewStore(products []Product) *Store
func (s *Store) Reserve(requests []Request) error
```

В программе должны быть sentinel-ошибки:

```go
ErrStoreUnavailable
ErrEmptyRequest
ErrUnknownProduct
ErrInvalidQuantity
ErrNotEnoughStock
```

### Входные данные и результат

Начальные остатки: **чай — 5, кофе — 3**.

Первый набор запросов:

```go
[]Request{
    {Name: "чай", Quantity: 2},
    {Name: "чай", Quantity: 1},
    {Name: "кофе", Quantity: 1},
}
```

После успешного резервирования: **чай — 2, кофе — 2**.

Следующий набор:

```go
[]Request{
    {Name: "чай", Quantity: 1},
    {Name: "кофе", Quantity: 10},
}
```

Ожидается `ErrNotEnoughStock`, а остатки **по-прежнему 2 и 2**.

### Критерии приёмки

- `NewStore` создаёт свою мапу с независимыми копиями исходных товаров.
- Повторные запросы на один товар **суммируются**.
- Пустой список, нулевое или отрицательное количество, неизвестный товар и нехватка остатка возвращают соответствующие ошибки.
- Если `s == nil`, возвращается `ErrStoreUnavailable`, без паники.
- Причина ошибки сохраняется в цепочке; неудачный вызов вообще не меняет остатки.

> [!TIP]
> Сначала удобно собрать план списания в `map[string]int`, и только потом применить его к `Store`.

---

## Задача 3. Уведомление активных пользователей

### Условие

Нужно отправить один текст всем **активным** пользователям. Способ отправки может быть разным: печать в терминал или запись в журнал в памяти.

**Даны:**

```go
type User struct {
    Name    string
    Contact string
    Active  bool
}

type Sender interface {
    Send(user User, text string) error
}
```

Создайте `ConsoleSender` и `LogSender` с полем `Messages []string`.

Оба реализуют `Sender`.

Объявите:

```go
var ErrMissingContact = errors.New("контакт не задан")
```

**Реализуйте:**

```go
func (ConsoleSender) Send(user User, text string) error
func (s *LogSender) Send(user User, text string) error
func Notify(sender Sender, users []User, text string) error
```

### Входные данные и результат

- `ConsoleSender` печатает `Имя <контакт>: текст`.
- `LogSender` добавляет такую же строку в `Messages`.
- Если у активного пользователя пустой `Contact`, `Send` возвращает `ErrMissingContact`.
- `Notify` пропускает неактивных, останавливается при первой ошибке и оборачивает её, добавляя имя пользователя.

**Пример входных данных:**

```go
users := []User{
    {Name: "Анна", Contact: "anna@example.com", Active: true},
    {Name: "Борис", Contact: "", Active: false},
    {Name: "Клара", Contact: "klara@example.com", Active: true},
}
```

При:

```go
Notify(ConsoleSender{}, users, "занятие началось")
```

выводятся только Анна и Клара:

```text
Анна <anna@example.com>: занятие началось
Клара <klara@example.com>: занятие началось
```

### Критерии приёмки

- `ConsoleSender` реализует интерфейс значением, `*LogSender` — указателем.
- Неактивным пользователям метод `Send` не вызывается.
- При активации Бориса вызов возвращает ошибку, которую `errors.Is` распознаёт как `ErrMissingContact`.
- Если `sender == nil`, `Notify` возвращает ошибку, а не панику.

---

## Задача 4. Расчёт заказа через интерфейсы

### Условие

Нужно посчитать сумму заказа, используя **интерфейс для получения цен** и **интерфейс для скидки**. Начало и конец обработки пишутся в журнал.

### Готовые объявления

Их можно сразу скопировать:

```go
var ErrEmptyOrder = errors.New("пустой заказ")
var ErrUnknownProduct = errors.New("товар не найден")
var ErrInvalidQuantity = errors.New("неверное количество")
var ErrInvalidDiscount = errors.New("неверная скидка")

type Item struct {
    Name     string
    Quantity int
}

type Order struct {
    Number string
    Items  []Item
}

type Pricer interface {
    Price(name string) (int, error)
}

type Discount interface {
    Apply(total int) (int, error)
}

type Catalog struct {
    Prices map[string]int
}

type PercentDiscount struct {
    Percent int
}
```

### Реализуйте

```go
func (c Catalog) Price(name string) (int, error)

func (d PercentDiscount) Apply(total int) (int, error)

func Checkout(
    order Order,
    pricer Pricer,
    discount Discount,
    log *[]string,
) (total int, err error)
```

### Требования

- `Catalog.Price` возвращает цену из мапы; неизвестный товар — `ErrUnknownProduct`.
- `PercentDiscount.Apply` применяет скидку `Percent` от 0 до 100 целых процентов; другое значение — `ErrInvalidDiscount`.
- Расчёт целочисленный:

```go
total * (100 - Percent) / 100
```

- `Checkout` отклоняет:
  - пустой номер;
  - пустой список товаров — `ErrEmptyOrder`;
  - неположительное количество — `ErrInvalidQuantity`.
- При отсутствии `pricer` возвращается ошибка.
- Сумма — сумма `Price(name) * Quantity` по всем товарам.
- Скидка применяется после этого, только если `discount != nil`.
- Если возникает ошибка цены или скидки, `Checkout` возвращает **нулевую сумму** и оборачивает ошибку с контекстом.
- В `log`, если он не `nil`, записывается начало обработки, а через `defer` — итог: успешно или ошибка.

### Пример

Каталог:

```text
чай: 250
кофе: 480
```

Заказ `A-17`: два чая и один кофе; скидка 10%.

```text
Сумма без скидки: 980
Итоговая сумма: 882

Журнал:
заказ A-17: начало
заказ A-17: готов
```

Для заказа `A-18` с товаром `молоко`, которого нет в каталоге:

- итоговая сумма `0`;
- `errors.Is(err, ErrUnknownProduct) == true`;
- журнал содержит начало и описание ошибки.

### Готовый `main` для проверки

Добавьте его к объявлениям выше и к трём реализованным функциям:

```go
func main() {
    catalog := Catalog{Prices: map[string]int{
        "чай":  250,
        "кофе": 480,
    }}

    discount := PercentDiscount{Percent: 10}
    var log []string

    order := Order{
        Number: "A-17",
        Items: []Item{
            {Name: "чай", Quantity: 2},
            {Name: "кофе", Quantity: 1},
        },
    }

    total, err := Checkout(order, catalog, discount, &log)
    fmt.Println("сумма:", total, "ошибка:", err)

    badOrder := Order{
        Number: "A-18",
        Items: []Item{
            {Name: "молоко", Quantity: 1},
        },
    }

    total, err = Checkout(badOrder, catalog, discount, &log)
    fmt.Println(
        "сумма:",
        total,
        "товар не найден:",
        errors.Is(err, ErrUnknownProduct),
        "ошибка:",
        err,
    )

    fmt.Println("журнал:")
    for _, message := range log {
        fmt.Println(message)
    }
}
```

### Критерии приёмки

- `Checkout` не зависит от `Catalog` и `PercentDiscount`, только от `Pricer` и `Discount`.
- `ErrUnknownProduct`, `ErrInvalidQuantity`, `ErrInvalidDiscount` можно определить через `errors.Is` после оборачивания.
- При любой ошибке итоговая сумма `0`; журнал заканчивается сообщением об ошибке.
- `defer` делает итоговую запись даже при раннем `return`.
- Вызов с `discount == nil` работает без скидки; с `log == nil` — без журнала.

> [!TIP]
> Именованные результаты `(total int, err error)` позволяют отложенной функции в `defer` проверить окончательное значение `err`.

---

## Задача 5. Новый тип скидки без изменения `Checkout`

### Условие

В предыдущей задаче уже есть интерфейс `Discount` и функция `Checkout`.

Добавьте **новый тип скидки** так, чтобы код `Checkout` менять не пришлось.

```go
type FixedDiscount struct {
    Amount int
}
```

**Реализуйте:**

```go
func (d FixedDiscount) Apply(total int) (int, error)
```

### Входные данные и результат

- Если `Amount < 0` — `ErrInvalidDiscount`.
- Если `Amount > total` — результат `0` без ошибки.
- Иначе вернуть `total - Amount` без ошибки.

Примеры:

```text
FixedDiscount{Amount: 120}.Apply(980) -> 860, nil
FixedDiscount{Amount: 120}.Apply(100) -> 0, nil
FixedDiscount{Amount: -1}.Apply(980) -> 0, ErrInvalidDiscount
```

### Критерии приёмки

- `FixedDiscount` неявно реализует `Discount`.
- `Checkout` принимает новую скидку **без изменений своего кода**.
- Итог никогда не становится отрицательным.
- Отрицательная скидка возвращает ошибку.
