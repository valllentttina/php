# Лабораторная работа №4. Массивы и Функции

## Цель работы

Освоить работу с массивами в PHP, применяя различные операции: создание, добавление, удаление, сортировка и поиск. Закрепить навыки работы с функциями, включая передачу аргументов, возвращаемые значения и анонимные функции.

## Условие

### Задание 1. Работа с массивами

Разработать систему управления банковскими транзакциями с возможностью:

- добавления новых транзакций;
- удаления транзакций;
- сортировки транзакций по дате или сумме;
- поиска транзакций по описанию.

#### Задание 1.1. Подготовка среды
Включена строгая типизация:
```php
   <?php

   declare(strict_types=1);
   ```

#### Задание 1.2. Создание массива транзакций 

Создайте массив `$transactions`, содержащий информацию о банковских транзакциях. Каждая транзакция представлена в виде ассоциативного массива с полями:

- `id` – уникальный идентификатор транзакции;
- `date` – дата совершения транзакции (YYYY-MM-DD);
- `amount` – сумма транзакции;
- `description` – описание назначения платежа;
- `merchant` – название организации, получившей платеж.

**Массив:**
```php
$transactions = [
    [
        "id" => 1,
        "date" => "2019-01-01",
        "amount" => 100.00,
        "description" => "Payment for groceries",
        "merchant" => "SuperMart",
    ],
    [
        "id" => 2,
        "date" => "2020-02-15",
        "amount" => 75.50,
        "description" => "Dinner with friends",
        "merchant" => "Local Restaurant",
    ],
    [
        "id" => 3,
        "date" => "2021-03-10",
        "amount" => 50.25,
        "description" => "Online shopping",
        "merchant" => "Amazon",
    ],
];
```
#### Задание 1.3. Вывод списка транзакций

1. Используйте `foreach`, чтобы вывести список транзакций в HTML-таблице.
```php
<?php foreach ($transactions as $transaction): ?>

<tr>
<td><?= $transaction["id"] ?></td>
<td><?= $transaction["date"] ?></td>
<td><?= $transaction["amount"] ?></td>
<td><?= $transaction["description"] ?></td>
<td><?= $transaction["merchant"] ?></td>
<td><?= daysSinceTransaction($transaction["date"]) ?></td>
</tr>

<?php endforeach; ?>

```

#### Задание 1.4. Реализация функций

Создайте и используйте следующие функции:
1. Создайте функцию `calculateTotalAmount(array $transactions): float`, которая вычисляет общую сумму всех транзакций.
   - Выведите сумму всех транзакций в конце таблицы.
```php
function calculateTotalAmount(array $transactions): float {
    $total = 0;

    foreach ($transactions as $transaction) {
        $total += $transaction["amount"];
    }

    return $total;
}
```
2. Создайте функцию `findTransactionByDescription(string $descriptionPart)`, которая ищет транзакцию по части описания.
```php
function findTransactionByDescription(string $descriptionPart, array $transactions): array {
    $result = [];

    foreach ($transactions as $transaction) {
        if (stripos($transaction["description"], $descriptionPart) !== false) {
            $result[] = $transaction;
        }
    }

    return $result;
}
```
3. Создайте функцию `findTransactionById(int $id)`, которая ищет транзакцию по идентификатору.
   - Реализуйте данную функцию с помощью обычного цикла `foreach`.
 ```php

function findTransactionById(int $id, array $transactions): ?array {
    foreach ($transactions as $transaction) {
        if ($transaction["id"] === $id) {
            return $transaction;
        }
    }

    return null;
}
```
4. Создайте функцию `daysSinceTransaction(string $date): int`, которая возвращает количество дней между датой транзакции и текущим днем.
   - Добавьте в таблицу столбец с количеством дней с момента транзакции.
```php
function daysSinceTransaction(string $date): int {
    $transactionDate = new DateTime($date);
    $today = new DateTime();

    $diff = $today->diff($transactionDate);

    return $diff->days;
}

```
5. Создайте функцию `addTransaction(int $id, string $date, float $amount, string $description, string $merchant): void` для добавления новой транзакции.
   - Примите во внимание, что массив `$transactions` должен быть доступен внутри функции как глобальная переменная.
```php
function addTransaction(int $id, string $date, float $amount, string $description, string $merchant): void {
    global $transactions;

    $transactions[] = [
        "id" => $id,
        "date" => $date,
        "amount" => $amount,
        "description" => $description,
        "merchant" => $merchant,
    ];
}
```
#### Задание 1.5. Сортировка транзакций

1. Отсортируйте транзакции по дате с использованием `usort()`.
```php
usort($transactions, function ($a, $b) {
    return strtotime($a["date"]) - strtotime($b["date"]);
});
```   
2. Отсортируйте транзакции по сумме (по убыванию).
```php
usort($transactions, function ($a, $b) {
    return $b["amount"] <=> $a["amount"];
});

```

**Фото выполненного задания:**

<img width="745" height="278" alt="image" src="https://github.com/user-attachments/assets/e505ae3d-c183-4e7b-af3b-16e4687737b3" />

### Задание 2. Работа с файловой системой

1. Создайте директорию `"image"`, в которой сохраните не менее 20-30 изображений с расширением .jpg.
2. Затем создайте файл `index.php`, в котором определите веб-страницу с хедером, меню, контентом и футером.
3. Выведите изображения из директории `"image"` на веб-страницу в виде галереи.

**Фото выполненного задания**
<img width="1879" height="800" alt="image" src="https://github.com/user-attachments/assets/475b0e97-a778-46ea-8c54-d4c87ce93c7a" />

**Код задания:**
```php
<?php

if ($files !== false){
for ($i = 0; $i < count($files); $i++) {
if ($files[$i] != "." && $files[$i] != "..") {
$path = $dir . $files[$i];
?>
<img src="<?php echo $path; ?>">
<?php
```
# Контрольные вопросы

### 1. Что такое массивы в PHP?

Массив — это структура данных, которая позволяет хранить несколько значений в одной переменной. 
Элементы массива могут иметь числовые или строковые ключи и использоваться для хранения связанных данных.

### 2. Каким образом можно создать массив в PHP?

Массив можно создать с помощью конструкции `array()` или с использованием короткого синтаксиса квадратных скобок `[]`.

### 3. Для чего используется цикл `foreach`?

Цикл `foreach` используется для перебора элементов массива. 
Он позволяет последовательно получать доступ к каждому элементу массива без необходимости использовать индексы.




