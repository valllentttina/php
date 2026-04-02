# Лабораторная работа №5. Объектно-ориентированное программирование в PHP

## Цель работы

Освоить основы объектно-ориентированного программирования в PHP на практике. 
Научиться создавать собственные классы, использовать инкапсуляцию для защиты данных, разделять ответственность между классами,
а также применять интерфейсы для построения гибкой архитектуры приложения.

## Условие

Необходимо разработать приложение для управления банковскими транзакциями.

Приложение должно позволять:

1. хранить банковские транзакции;
2. добавлять новые транзакции;
3. удалять транзакции;
4. искать транзакции;
5. сортировать транзакции;
6. выполнять вычисления над коллекцией транзакций;
7. выводить данные в виде HTML-таблицы.

В рамках лабораторной работы необходимо использовать объектно-ориентированный подход.

Все классы можно разместить:
- либо в отдельных файлах, если вы хотите организовать структуру проекта аккуратнее.

### Задание 1. Включение строгой типизации

В начале файла включите строгую типизацию:

```php
<?php

declare(strict_types=1);
```
<img width="429" height="93" alt="image" src="https://github.com/user-attachments/assets/6c86124c-f899-43d0-b168-112d55e23bb7" />

### Задание 2. Класс `Transaction`

Создайте класс `Transaction`, который описывает одну банковскую транзакцию.
Класс должен содержать следующие свойства:

- `id` — уникальный идентификатор транзакции;
- `date` — дата транзакции;
- `amount` — сумма транзакции;
- `description` — описание платежа;
- `merchant` — получатель платежа.

Класс должен содержать метод: `getDaysSinceTransaction(): int`,
который возвращает количество дней с момента транзакции до текущей даты.

_Требования_:

1. Все свойства должны быть приватными.
2. Значения свойств должны задаваться через конструктор.
3. Для получения данных создайте getter-методы.

> [!TIP]
>
> Для работы с датами используйте класс `DateTime`.
```php
<?php
declare(strict_types=1);

class Transaction
{
    private int $id;
    private DateTime $date;
    private float $amount;
    private string $description;
    private string $merchant;
    public function __construct(
        int    $id,
        string $date,
        float  $amount,
        string $description,
        string $merchant
    )
    {
        $this->id = $id;
        $this->date = new DateTime($date);
        $this->amount = $amount;
        $this->description = $description;
        $this->merchant = $merchant;
    }
    public function getId(): int
    {
        return $this->id;
    }
    public function getDate(): DateTime
    {
        return $this->date;
    }
    public function getAmount(): float
    {
        return $this->amount;
    }
    public function getDescription(): string
    {
        return $this->description;
    }
    public function getMerchant(): string
    {
        return $this->merchant;
    }
    public function getDaysSinceTransaction(): int
    {
        $currentDate = new DateTime();
        $difference = $this->date->diff($currentDate);
        return (int)$difference->days;
    }
}
```
### Задание 3. Класс `TransactionRepository`

Создайте класс `TransactionRepository`, который будет управлять коллекцией транзакций. Этот класс должен отвечать только за хранение данных и базовые операции доступа к ним.

_Класс должен_:

1. хранить массив объектов Transaction;
2. добавлять новые транзакции;
   - `addTransaction(Transaction $transaction): void`
3. удалять транзакции по идентификатору;
   - `removeTransactionById(int $id): void`
4. возвращать полный список транзакций;
   - `getAllTransactions(): array`
5. находить транзакцию по id.
   - `findById(int $id): ?Transaction`

_Требования_:

1. Массив транзакций должен быть приватным свойством класса.
2. Доступ к данным должен осуществляться только через методы класса.
3. Не допускается прямой доступ к массиву транзакций извне.
4. Для методов и свойств используйте строгую типизацию.
```php
<?php
declare(strict_types=1);

require_once 'Transaction.php';
require_once 'TransactionStorageInterface.php';

class TransactionRepository implements TransactionStorageInterface
{
    private array $transactions = [];

    public function addTransaction(Transaction $transaction): void
    {
        $this->transactions[] = $transaction;
    }

    public function removeTransactionById(int $id): void
    {
        foreach ($this->transactions as $index => $transaction) {
            if ($transaction->getId() === $id) {
                unset($this->transactions[$index]);
                $this->transactions = array_values($this->transactions);
                return;
            }
        }
    }

    public function getAllTransactions(): array
    {
        return $this->transactions;
    }

    public function findById(int $id): ?Transaction
    {
        foreach ($this->transactions as $transaction) {
            if ($transaction->getId() === $id) {
                return $transaction;
            }
        }

        return null;
    }
}
```
### Задание 4. Класс `TransactionManager`

Создайте класс `TransactionManager`, который будет использовать `TransactionRepository` для выполнения бизнес-логики.

`TransactionManager` не должен создавать транзакции самостоятельно и не должен хранить их внутри себя. Объект `TransactionRepository` необходимо передать в `TransactionManager` через конструктор:

```php
public function __construct(
    private TransactionRepository $repository
) {
}
```

_Класс должен реализовать следующие функции_:

1. вычисление общей суммы всех транзакций;
   - `calculateTotalAmount(): float`
2. вычисление суммы транзакций за определенный период;
   - `calculateTotalAmountByDateRange(string $startDate, string $endDate): float`
3. подсчет количества транзакций по определенному получателю;
   - `countTransactionsByMerchant(string $merchant): int`
4. сортировку транзакций по дате;
   - `sortTransactionsByDate(): Transaction[]`
5. сортировку транзакций по сумме по убыванию.
   - `sortTransactionsByAmountDesc(): Transaction[]`

_Требования_:

1. `TransactionManager` не должен хранить транзакции самостоятельно.
2. Для получения данных он должен обращаться к объекту репозитория.
3. В классе должны быть методы для каждой из перечисленных функций.
4. _При необходимости создавайте приватные вспомогательные методы_.
```php
<?php
declare(strict_types=1);

require_once 'Transaction.php';
require_once 'TransactionStorageInterface.php';

class TransactionManager
{
    public function __construct(
        private TransactionStorageInterface $repository
    ) {
    }

    public function calculateTotalAmount(): float
    {
        $total = 0;

        foreach ($this->repository->getAllTransactions() as $transaction) {
            $total += $transaction->getAmount();
        }

        return $total;
    }

    public function calculateTotalAmountByDateRange(string $startDate, string $endDate): float
    {
        $start = new DateTime($startDate);
        $end = new DateTime($endDate);
        $total = 0;

        foreach ($this->repository->getAllTransactions() as $transaction) {
            $transactionDate = $transaction->getDate();

            if ($transactionDate >= $start && $transactionDate <= $end) {
                $total += $transaction->getAmount();
            }
        }

        return $total;
    }

    public function countTransactionsByMerchant(string $merchant): int
    {
        $count = 0;

        foreach ($this->repository->getAllTransactions() as $transaction) {
            if (mb_strtolower($transaction->getMerchant()) === mb_strtolower($merchant)) {
                $count++;
            }
        }

        return $count;
    }

    public function sortTransactionsByDate(): array
    {
        $transactions = $this->repository->getAllTransactions();

        usort($transactions, function (Transaction $a, Transaction $b): int {
            return $a->getDate()->getTimestamp() <=> $b->getDate()->getTimestamp();
        });

        return $transactions;
    }

    public function sortTransactionsByAmountDesc(): array
    {
        $transactions = $this->repository->getAllTransactions();

        usort($transactions, function (Transaction $a, Transaction $b): int {
            return $b->getAmount() <=> $a->getAmount();
        });

        return $transactions;
    }
}
```
### Задание 5. Класс `TransactionTableRenderer`

Создайте отдельный класс `TransactionTableRenderer`, который отвечает только за вывод транзакций в HTML. Этот класс должен получать список транзакций и формировать HTML-таблицу.

_Класс должен реализовать следующие функции_:

- `render(array $transactions): string` — принимает массив транзакций и возвращает строку с HTML-кодом таблицы.

_Метод должен возвращать HTML-таблицу со следующими столбцами_:

- ID транзакции;
- дата;
- сумма;
- описание;
- название получателя;
- категория получателя;
- количество дней с момента транзакции.

_Требования_:

- HTML-код должен генерироваться внутри класса.
- В основном файле должен выполняться только вызов метода `render()` и вывод результата через echo.
- Класс рекомендуется объявить как `final`.
```php
<?php
declare(strict_types=1);

require_once 'Transaction.php';

final class TransactionTableRenderer
{
    public function render(array $transactions): string
    {
        $html = '<table border="1" cellpadding="8" cellspacing="0">';
        $html .= '<tr>';
        $html .= '<th>ID транзакции</th>';
        $html .= '<th>Дата</th>';
        $html .= '<th>Сумма</th>';
        $html .= '<th>Описание</th>';
        $html .= '<th>Название получателя</th>';
        $html .= '<th>Категория получателя</th>';
        $html .= '<th>Количество дней с момента транзакции</th>';
        $html .= '</tr>';

        foreach ($transactions as $transaction) {
            $html .= '<tr>';
            $html .= '<td>' . $transaction->getId() . '</td>';
            $html .= '<td>' . htmlspecialchars($transaction->getDate()->format('Y-m-d')) . '</td>';
            $html .= '<td>' . number_format($transaction->getAmount(), 2) . '</td>';
            $html .= '<td>' . htmlspecialchars($transaction->getDescription()) . '</td>';
            $html .= '<td>' . htmlspecialchars($transaction->getMerchant()) . '</td>';
            $html .= '<td>' . htmlspecialchars($this->getMerchantCategory($transaction->getMerchant())) . '</td>';
            $html .= '<td>' . $transaction->getDaysSinceTransaction() . '</td>';
            $html .= '</tr>';
        }

        $html .= '</table>';

        return $html;
    }

    private function getMerchantCategory(string $merchant): string
    {
        $categories = [
            'Supermarket Bonus' => 'Супермаркет',
            'Pharmacy Health' => 'Аптека',
            'Online Store Tech' => 'Техника',
            'Gas Station Fuel' => 'Топливо',
            'Restaurant вкусно' => 'Ресторан',
            'Book Shop' => 'Книги',
            'Cinema City' => 'Развлечения',
            'Mobile Provider' => 'Связь',
            'Clothing Mall' => 'Одежда',
            'Utility Services' => 'Коммунальные услуги',
        ];

        if (isset($categories[$merchant])) {
            return $categories[$merchant];
        }

        return 'Другое';
    }
}
```
### Задание 7. Интерфейс `TransactionStorageInterface`

После завершения основной реализации сделайте архитектуру более гибкой.

Создайте интерфейс `TransactionStorageInterface`.

Интерфейс должен содержать методы:

1. `addTransaction(Transaction $transaction): void`
2. `removeTransactionById(int $id): void`
3. `getAllTransactions(): array`
4. `findById(int $id): ?Transaction`

_Требования_:

- `TransactionRepository` должен реализовывать интерфейс.
- `TransactionManager` должен принимать через конструктор уже интерфейс, а не конкретный класс.

```php
<?php
declare(strict_types=1);

interface TransactionStorageInterface
{
    public function addTransaction(Transaction $transaction): void;

    public function removeTransactionById(int $id): void;

    public function getAllTransactions(): array;

    public function findById(int $id): ?Transaction;
}
```
## Результат
<img width="838" height="919" alt="image" src="https://github.com/user-attachments/assets/8dd5af73-291c-465c-af0c-60fcd5436ed3" />
<img width="790" height="710" alt="image" src="https://github.com/user-attachments/assets/e914187a-885b-44a0-afa1-87a5638ecce8" />

## Контрольные вопросы
1. строгая типизация — это когда php строго проверяет типы данных (int, string и т.д.).
помогает избежать ошибок, например чтобы в число не передали текст.

2. класс — это шаблон для создания объектов. он содержит свойства (переменные) и методы (функции), а также конструктор.

3. полиморфизм — это когда один и тот же метод работает по-разному. в php это можно сделать через интерфейсы или наследование,
когда разные классы реализуют один и тот же метод по-своему.

4. интерфейс — это набор методов без реализации. класс обязан их реализовать. 
отличие от абстрактного класса в том, что интерфейс не содержит кода, а только объявления методов.

5. интерфейсы делают код гибким. например в этой работе TransactionManager работает не с конкретным классом, а с интерфейсом.
благодаря этому можно легко заменить способ хранения данных (например база данных вместо массива) без изменения логики программы.


