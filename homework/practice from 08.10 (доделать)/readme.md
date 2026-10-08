
- 1
<img width="1529" height="863" alt="1" src="https://github.com/user-attachments/assets/5589601c-cc99-4b36-8b49-11767590f64e" />



- 3
<img width="579" height="734" alt="Снимок экрана 2026-10-08 135803" src="https://github.com/user-attachments/assets/9c234847-a10a-4172-b66b-f6573a5d1e22" />

- 2
<img width="649" height="693" alt="Снимок экрана 2026-10-08 135639" src="https://github.com/user-attachments/assets/a6786aea-1941-4490-bcfc-a81d5d85cc17" />


- 5
<img width="969" height="966" alt="4" src="https://github.com/user-attachments/assets/1c650388-e8b9-48aa-80b6-01dbf63ec7a1" />

- 4
<img width="932" height="740" alt="2" src="https://github.com/user-attachments/assets/e9ee9fc2-4488-45bd-ab62-2940ae6e1d5f" />


---

## Новые скриншоты окон из нового sql 22 года (ИЗ ПРЕЗЕНТАЦИИ "восстановление")**

- Скриншот со слайда 10: <img width="2558" height="1438" alt="Снимок экрана 2026-10-08 165350" src="https://github.com/user-attachments/assets/07485b92-ecef-43e1-b273-6cc2fd6e58b7" />

- Скриншот со слайда 23:  <img width="2558" height="1438" alt="232111111111" src="https://github.com/user-attachments/assets/a11b965f-3b17-4055-990c-1dcad12d20b0" />

- Скриншот со слайда 24: <img width="2286" height="845" alt="12" src="https://github.com/user-attachments/assets/9d8d7566-9389-454e-960f-781088770572" />

- Скриншот со слайда 45 - нету! В SQL Server 22 планы обслуживания (Maintenance Plans) **недоступны** через интерфейс из-за отсутствия компонента SQL Server Agent. В таких случаях задачи автоматизации (проверка целостности DBCC CHECKDB и резервное копирование BACKUP DATABASE) реализуются с помощью T-SQL скриптов <img width="2578" height="1454" alt="6" src="https://github.com/user-attachments/assets/4b0334cf-f6b0-4e30-b790-ee8fced69933" />

**Скрипт для имитации плана обслуживания**
```
-- 1. Проверка внутренней целостности базы данных
-- (Аналог первой галочки в графическом мастере планов обслуживания)
PRINT 'Запуск проверки целостности базы данных...';
DBCC CHECKDB ('test') WITH NO_INFOMSGS;
GO

-- 2. Создание полной резервной копии базы данных
-- (Аналог задачи резервного копирования со слайда лекции)
-- Файл бэкапа сохраняется на диск C:\ для упрощения пути доступа
PRINT 'Запуск создания полного резервного копирования...';
BACKUP DATABASE [test] 
TO DISK = N'C:\test_full_backup.bak' 
WITH FORMAT, 
INIT, 
NAME = N'Полный бэкап базы test для лабораторной', 
STATS = 10;
GO
```


- Скриншот со слайда 49 - компонент SQL Server Agent в SQL 2022 и графические интерфейсы (вкладки Alert System / Система предупреждений) заблокированы. Но обычно привязка почтовых профилей и рассылка алертов реализуются напрямую через подсистему Database Mail с использованием встроенной процедуры msdb.dbo.sp_send_dbmail, минуя службу Агента.
  
**Скрипт для имитации настройки Агента**
```sql
USE [master];
GO

-- 1. АКТИВАЦИЯ КОМПОНЕНТА DATABASE MAIL НА СЕРВЕРЕ 2022
-- Разрешаем изменение расширенных конфигурационных параметров СУБД
EXEC sp_configure 'show advanced options', 1;
RECONFIGURE;
GO
-- Включаем использование функционала Database Mail (XPs)
EXEC sp_configure 'Database Mail XPs', 1;
RECONFIGURE;
GO

-- 2. ИМИТАЦИЯ НАСТРОЙКИ ОКНА ПРЕДУПРЕЖДЕНИЙ (ОТПРАВКА ТЕСТОВОГО АЛЕРТА)

--  Заменить  'MyEmailProfile' на имя реального профиля, созданного через Management -> Database Mail, а также указать свой Email.

EXEC msdb.dbo.sp_send_dbmail
    @profile_name = 'MyEmailProfile', 
    @recipients = 'admin_mail@domain.ru',
    @body = 'Уведомление: Проверка подсистемы оповещений и Database Mail на SQL Server 2022 прошла успешно.',
    @subject = 'Тестовое оповещение СУБД (Имитация Alert System)';
GO
```

- Скриншот со слайда 50 - <img width="1625" height="552" alt="232323232323232323232323232323232323232323" src="https://github.com/user-attachments/assets/51824f2d-e9c5-4786-bd76-6252dd89dcc7" />

- Скриншот со слайда 67 - нету интерфейса Database Mail в 22 версии SQL Managment. Но можно через код: 
   **Скрипт настройки и активации почты** - принудительно включает почтовую службу в настройках сервера, создает базовый профиль и отправляет проверочное сообщение

```sql
-- ШАГ 1: Включаем отправку почты на уровне сервера
EXEC sp_configure 'show advanced options', 1;
RECONFIGURE;
GO
EXEC sp_configure 'Database Mail XPs', 1;
RECONFIGURE;
GO

-- ШАГ 2: Создаем простой почтовый профиль
EXEC msdb.dbo.sysmail_add_profile_sp
    @profile_name = 'SimpleMailProfile',
    @description = 'Профиль для отправки уведомлений';
GO

-- ШАГ 3: Проверка работы (Имитация кнопки "Отправить тестовое письмо")
EXEC msdb.dbo.sp_send_dbmail
    @profile_name = 'SimpleMailProfile',
    @recipients = 'admin_mail@test.ru', -- Почта получателя
    @subject = 'Тестовое уведомление СУБД',
    @body = 'Компонент Database Mail на SQL Server 2022 Express успешно настроен кодом T-SQL.';
GO
```

