# Лабораторная работа: Транзакции и уровни изоляции в T-SQL

В репозитории примеры выполнения запросов, демонстрации механизмов транзакций, блокировок и различных уровней изоляции транзакций в T-SQL.

---

## Слайд 16: BEGIN TRANSACTION и COMMIT

Этот слайд демонстрирует базовую транзакцию и фиксацию внесённых изменений командой `COMMIT`. Команда `BEGIN TRANSACTION` открывает блок операций, а `COMMIT` окончательно сохраняет данные в таблице, делая их постоянными и видимыми для всех сессий.

<p align="center">
  <img src="https://github.com/user-attachments/assets/a739f686-c936-44dc-8c80-71c2c877f633" alt="Слайд 16" width="100%" />
</p>

---

## Слайд 17: ROLLBACK

 показан пример полной отмены всех действий внутри транзакции с помощью команды `ROLLBACK`. Это необходимо при возникновении сбоев или ошибок логики, чтобы вернуть базу данных к исходному состоянию до выполнения `BEGIN TRANSACTION`.

<p align="center">
  <img src="https://github.com/user-attachments/assets/cdfdeae1-0094-4d94-a60e-ec338750c7db" alt="Слайд 17" width="100%" />
</p>

---

## Слайд 18: SAVE TRANSACTION (Точки сохранения)

Тут показано использование контрольных точек для частичного отката операций внутри транзакции. Команда `SAVE TRANSACTION <имя>` ставит промежуточную метку, и через `ROLLBACK TRANSACTION <имя>` отменяем только определённые шаги, не прерывая всю транзакцию целиком.

<p align="center">
  <img src="https://github.com/user-attachments/assets/9d82c7a2-3bc9-48ce-bd55-4b53b769b396" alt="Слайд 18" width="100%" />
</p>

---

## Слайд 23: Системная функция @@TRANCOUNT

На слайде показано отслеживание уровня вложенности транзакций с помощью функции `@@TRANCOUNT`. Каждый вызов `BEGIN TRANSACTION` увеличивает этот счётчик на 1, а `COMMIT` уменьшает на 1, позволяя скриптам определять наличие активной открытой транзакции.

<p align="center">
  <img src="https://github.com/user-attachments/assets/1e95c2ff-de80-4fc1-8e4c-bf4ed00f9da0" alt="Слайд 23" width="100%" />
</p>

---

## Слайд 33: Системное представление sys.dm_tran_locks

Слайд посвящён мониторингу текущих блокировок в базе данных через представление `sys.dm_tran_locks`.

<p align="center">
  <img src="https://github.com/user-attachments/assets/e99e0d5f-6417-472b-b774-71bc4a4bdcf3" alt="Слайд 33" width="100%" />
</p>

---

## Слайд 35: Взаимоблокировки (Deadlocks)

Здесь ситуация взаимной блокировки (дедлока), когда две параллельные транзакции ожидают освобождения ресурсов, занятых друг другом. SQL автоматически выявляет возникший тупик, прерывает одну из транзакций как жертву (`deadlock victim`) и возвращает ошибку с кодом 1205.

<p align="center">
  <img src="https://github.com/user-attachments/assets/9b0fd5a4-8026-40a1-8188-2dbc52f9d104" alt="Слайд 35" width="100%" />
</p>

---

## Слайды 45–46: Уровень изоляции READ UNCOMMITTED («Грязное чтение»)

Эти слайды демонстрируют эффект «грязного чтения» (Dirty Read) на минимальном уровне изоляции транзакций `READ UNCOMMITTED`. Запрос на чтение возвращает промежуточные данные параллельной незавершённой транзакции, которые после выполнения `ROLLBACK` бесследно исчезают.

### Шаг 1–3: Чтение неподтверждённых изменений
<p align="center">
  <img src="https://github.com/user-attachments/assets/df65e993-8bfe-4bf3-b41c-b54756d3f3c5" alt="Слайд 45" width="100%" />
</p>

### Шаг 4–5: Откат транзакции (ROLLBACK) и возвращение прежнего значения
<p align="center">
  <img src="https://github.com/user-attachments/assets/1b55fdc8-b7d8-49c8-bcd0-94d242d85973" alt="Слайд 46" width="100%" />
</p>

---

## Слайд 48: Уровень изоляции READ COMMITTED (Защита от грязного чтения)

Этот слайд демонстрирует стандартный уровень изоляции `READ COMMITTED`, предотвращающий чтение незафиксированных данных. При попытке прочитать строку параллельный запрос переходит в режим ожидания до тех пор, пока изменяющая транзакция не завершится через `COMMIT` или `ROLLBACK`.

<p align="center">
  <img src="https://github.com/user-attachments/assets/46d51b50-08ff-4e42-8d4f-1ae4f9d0bf6e" alt="Слайд 48 - часть 1" width="100%" />
</p>
<p align="center">
  <img src="https://github.com/user-attachments/assets/78c12438-b9c4-46b7-b4b5-6d4589d3f9d0" alt="Слайд 48 - часть 2" width="100%" />
</p>

---

## Слайд 49: READ COMMITTED и неповторяющееся чтение (Non-repeatable Read)

Этот слайд показывает, что уровень `READ COMMITTED` допускает аномалию неповторяющегося чтения при изменении строк другими транзакциями. Вторая транзакция удаляет и сразу подтверждает удаление строки, из-за чего повторный `SELECT` в рамках одной сессии выдаёт изменившееся количество записей.

<p align="center">
  <img src="https://github.com/user-attachments/assets/69dd39ce-2393-4004-959d-37ce90ddf7c3" alt="Слайд 49 - часть 1" width="100%" />
</p>
<p align="center">
  <img src="https://github.com/user-attachments/assets/168e20a8-b217-4acc-a980-4172d68251e8" alt="Слайд 49 - часть 2" width="100%" />
</p>

---

## Слайд 51: Уровень изоляции REPEATABLE READ (Блокировка изменений)

Слайд иллюстрирует решение проблемы неповторяющегося чтения с помощью уровня `REPEATABLE READ`. СУБД удерживает разделяемые блокировки (Shared Locks) на прочитанные записи до окончания всей транзакции, блокируя попытки других транзакций изменить или удалить эти данные.

<p align="center">
  <img src="https://github.com/user-attachments/assets/f2886581-8b7e-4820-acde-79a58808eb65" alt="Слайд 51 - часть 1" width="100%" />
</p>
<p align="center">
  <img src="https://github.com/user-attachments/assets/063ef1c8-02ad-41a4-b58c-98d06490f996" alt="Слайд 51 - часть 2" width="100%" />
</p>

---

## Слайд 52: REPEATABLE READ и фантомное чтение (Phantom Read)

Здесь показано ограничение уровня `REPEATABLE READ`, который защищает существующие строки от правок, но не защищает от вставки новых «фантомных» строк. Сторонняя транзакция успешно выполняет команду `INSERT`, поэтому при повторном подсчёте строк их количество оказывается больше.

<p align="center">
  <img src="https://github.com/user-attachments/assets/3428036b-042e-4217-8720-cc94f63fd664" alt="Слайд 52 - часть 1" width="100%" />
</p>
<p align="center">
  <img src="https://github.com/user-attachments/assets/30a3400a-0b2d-4770-9203-b43f70666ce1" alt="Слайд 52 - часть 2" width="100%" />
</p>
<p align="center">
  <img src="https://github.com/user-attachments/assets/32090c1d-f46b-4145-b745-c1310b4946b4" alt="Слайд 52 - часть 3" width="100%" />
</p>

---

## Слайд 55: Уровень изоляции SERIALIZABLE (Защита от фантомов)

Этот слайд демонстрирует работу самого строгого уровня изоляции `SERIALIZABLE`, полностью исключающего возникновение фантомных записей. T-SQL применяет диапазонные блокировки (Key-Range Locks), благодаря чему параллельная вставка новой записи блокируется до момента завершения первой транзакции.

<p align="center">
  <img src="https://github.com/user-attachments/assets/2763f23a-afef-4c87-82f9-1de73f82ea49" alt="Слайд 55 - часть 1" width="100%" />
</p>
<p align="center">
  <img src="https://github.com/user-attachments/assets/cac26184-7033-4cf5-b099-daf733f8fdb9" alt="Слайд 55 - часть 2" width="100%" />
</p>
<p align="center">
  <img src="https://github.com/user-attachments/assets/77ee8a72-33c3-47f9-ae90-fa3a38eeedbc" alt="Слайд 55 - часть 3" width="100%" />
</p>

---

## Слайды 64–65: Уровень изоляции SNAPSHOT

Эти слайды демонстрируют использование моментальных снимков данных (`SNAPSHOT`) на основе версионирования строк в `tempdb`. Транзакция читает согласованное состояние таблицы на момент своего старта без наложения блокировок чтения, а изменения из параллельных транзакций становятся видны только после завершения текущей сессии.

### Слайд 64: Включение опции и чтение снимка
<p align="center">
  <img src="https://github.com/user-attachments/assets/9ae6b373-58d9-4a62-afac-18e5c305d13e" alt="Слайд 64 - часть 1" width="100%" />
</p>
<p align="center">
  <img src="https://github.com/user-attachments/assets/ea60803e-181d-4f06-83d5-399bc45026f3" alt="Слайд 64 - часть 2" width="100%" />
</p>
<p align="center">
  <img src="https://github.com/user-attachments/assets/4032d0bb-c9ea-49d1-a80c-616d8d5988ef" alt="Слайд 64 - часть 3" width="100%" />
</p>
<p align="center">
  <img src="https://github.com/user-attachments/assets/ea4f4de7-1009-44f9-82a4-97324f4c6ec9" alt="Слайд 64 - часть 4" width="100%" />
</p>

### Слайд 65: Завершение SNAPSHOT-транзакции
<p align="center">
  <img src="https://github.com/user-attachments/assets/98284a73-1468-4b3c-9aa1-b9e29549165e" alt="Слайд 65" width="100%" />
</p>
